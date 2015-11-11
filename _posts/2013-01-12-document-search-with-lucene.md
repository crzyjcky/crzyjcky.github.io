---
title: Simple Document Search with Lucene
author: admin
layout: post
permalink: /2013/01/12/document-search-with-lucene/
categories:
  - Java
  - Lucene
tags:
  - lucene
---
The way Apache Lucene speed up document search is by searching through special crafted index, instead of opening documents one-by-one during run-time. Using Lucene is a two-step process: creating index, and searching document (via index).

# Part 1: Creating Index

You have to put all the text files in directory specified by &#8220;DATA\_DIR&#8221;. This program will generates index files and stores them inside &#8220;INDEX\_DIR&#8221; directory.

<pre class="brush: java; title: ; notranslate" title="">package com.crzyjcky.blog;

import java.io.File;
import java.io.FileReader;
import java.io.IOException;

import org.apache.lucene.analysis.standard.StandardAnalyzer;
import org.apache.lucene.document.Document;
import org.apache.lucene.document.Field;
import org.apache.lucene.index.CorruptIndexException;
import org.apache.lucene.index.IndexWriter;
import org.apache.lucene.store.Directory;
import org.apache.lucene.store.FSDirectory;
import org.apache.lucene.util.Version;


public class Indexer {

	private static final String INDEX_DIR = "index";
	private static final String DATA_DIR = "data";
	
	private IndexWriter indexWriter;
	
	public void index(String indexDir, String dataDir) throws IOException {
		
		Directory dir = FSDirectory.open(new File(indexDir));
		indexWriter = new IndexWriter(dir, new StandardAnalyzer(Version.LUCENE_30), true, IndexWriter.MaxFieldLength.UNLIMITED);
		
		File[] files = new File(DATA_DIR).listFiles();
		for (File file : files) {
			
			Document doc = new Document();
			doc.add(new Field("contents", new FileReader(file)));
			doc.add(new Field("filename", file.getName(), Field.Store.YES, Field.Index.NOT_ANALYZED));
			
			indexWriter.addDocument(doc);
			System.out.println("doc added: " + file.getName());
		}
	}
	
	public void close() throws CorruptIndexException, IOException {
		
		indexWriter.close();
	}
	
	/**
	 * @param args
	 * @throws IOException 
	 */
	public static void main(String[] args) throws IOException {
		
		System.out.println("start: " + System.currentTimeMillis());
		
		Indexer indexer = new Indexer();
		indexer.index(INDEX_DIR, DATA_DIR);
		indexer.close();
		
		System.out.println("end: " + System.currentTimeMillis());
	}
}
</pre>

## Part 2: Searching Documents

Note that Lucene search through indexes, not the actual documents.

<pre class="brush: java; title: ; notranslate" title="">package com.crzyjcky.blog;
import java.io.File;
import java.io.IOException;

import org.apache.lucene.analysis.standard.StandardAnalyzer;
import org.apache.lucene.document.Document;
import org.apache.lucene.queryParser.ParseException;
import org.apache.lucene.queryParser.QueryParser;
import org.apache.lucene.search.IndexSearcher;
import org.apache.lucene.search.Query;
import org.apache.lucene.search.ScoreDoc;
import org.apache.lucene.search.TopDocs;
import org.apache.lucene.store.Directory;
import org.apache.lucene.store.FSDirectory;
import org.apache.lucene.util.Version;


public class Searcher {

	private static final String INDEX_DIR = "index";
	
	private static final int NUM_SEARCH = 10;
	
	public void search(String indexDir, String queryString) throws IOException, ParseException {
		
		Directory dir = FSDirectory.open(new File(INDEX_DIR));
		IndexSearcher indexSearcher = new IndexSearcher(dir);
		
		QueryParser queryParser = new QueryParser(Version.LUCENE_30, "contents", new StandardAnalyzer(Version.LUCENE_30));
		Query query = queryParser.parse(queryString);
		
		TopDocs hits = indexSearcher.search(query, NUM_SEARCH);
		
		System.out.println("#hits: " + hits.totalHits);
		
		for (ScoreDoc scoreDoc : hits.scoreDocs) {
			
			System.out.print("scoreDoc.doc: " + scoreDoc.doc + ", score: " + scoreDoc.score + " ");
			Document doc = indexSearcher.doc(scoreDoc.doc);
			System.out.println("\tfilename: " + doc.get("filename"));
		}
		
		indexSearcher.close();
	}
	
	/**
	 * @param args
	 * @throws ParseException 
	 * @throws IOException 
	 */
	public static void main(String[] args) throws IOException, ParseException {
		
		System.out.println("start: " + System.currentTimeMillis());
		Searcher searcher  = new Searcher();
		
		// put your query here
		String query = "Redistribution";
		
		searcher.search(INDEX_DIR, query);
		
		System.out.println("end: " + System.currentTimeMillis());
	}
}
</pre>

Source code: <a href="http://crzyjcky.com/2013/01/12/document-search-with-lucene/blog-lucene/" rel="attachment wp-att-146">blog-lucene</a>  
<SCRIPT charset="utf-8" type="text/javascript" src="http://ws.amazon.com/widgets/q?rt=tf_mfw&#038;ServiceVersion=20070822&#038;MarketPlace=US&#038;ID=V20070822/US/crzyjcky-20/8001/e8b36acf-a008-41eb-8ada-8779a74b2e9a"> </SCRIPT> 

<NOSCRIPT>
  <A HREF="http://ws.amazon.com/widgets/q?rt=tf_mfw&#038;ServiceVersion=20070822&#038;MarketPlace=US&#038;ID=V20070822%2FUS%2Fcrzyjcky-20%2F8001%2Fe8b36acf-a008-41eb-8ada-8779a74b2e9a&#038;Operation=NoScript">Amazon.com Widgets</A>
</NOSCRIPT>