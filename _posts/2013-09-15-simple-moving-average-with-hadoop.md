---
title: Simple Moving Average with Hadoop
author: admin
layout: post
permalink: /2013/09/15/simple-moving-average-with-hadoop/
categories:
  - Big Data
  - Hadoop
  - Java
  - Uncategorized
---
Given

<pre class="brush: plain; title: ; notranslate" title="">y(t) = [x(t) + x(t - 1) + x(t - 2) + ... + x(t - N + 1)] / N

where 
t = time,
N = number of values / points for computation.
</pre>

From the equation, each x(t) will be used in N calculations,

<pre class="brush: plain; title: ; notranslate" title="">y(t) = [x(t) + x(t - 1) + x(t - 2) + ... + x(t - N + 1)] / N
y(t + 1) = [x(t + 1) + x(t) + x(t - 1) + ... + x(t - N + 2)] / N
y(t + 2) = [x(t + 2) + x(t + 1) + x(t) + ... + x(t - N + 3)] / N
:
y(t + N - 1) = [x(t + N - 1) + x(t + N - 2) + x(t + N - 3) + ... + x(t)] / N
</pre>

To get a input data (time-series data) for our tutorial, we can visit <http://finance.yahoo.com/q/hp?s=YHOO> and download it.

<pre class="brush: java; title: ; notranslate" title="">package com.company;

import java.io.IOException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.FloatWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;
import org.apache.hadoop.util.StringUtils;
import org.joda.time.DateTime;
import org.joda.time.format.DateTimeFormat;
import org.joda.time.format.DateTimeFormatter;

public class Main {

	private static final int N = 5;

	public static class MovingAverageMapper extends Mapper&lt;LongWritable, Text, Text, FloatWritable&gt; {

		public void map(
				LongWritable key,
				Text value,
				Context context) throws IOException, InterruptedException {
			//
			// http://finance.yahoo.com/q/hp?s=YHOO
			// 
			// Date,Open,High,Low,Close,Volume,Adj Close
			// 2013-09-13,29.47,29.47,28.80,29.26,13836600,29.26
			// 2013-09-12,29.72,30.27,29.50,29.65,22018600,29.65
			// 2013-09-11,29.38,29.41,28.97,29.19,10345700,29.19
			// 2013-09-10,29.43,29.63,29.08,29.48,12992600,29.48
			// 2013-09-09,28.32,29.32,28.32,29.24,21167700,29.24
			// 2013-09-06,28.35,28.50,27.82,28.17,10807500,28.17
			// 2013-09-05,28.10,28.35,27.91,28.23,8989600,28.23
			//

			String line = value.toString();
			String[] cols = StringUtils.getStrings(line);

			String dateString = cols[0];
			Float adjClose = Float.parseFloat(cols[6]);

			// I use joda Time to calculate possible keys for each adjClose value 
			DateTimeFormatter dateTimeFormatter = DateTimeFormat.forPattern("yyyy-MM-dd");
			DateTime dateTime = DateTime.parse(dateString, dateTimeFormatter);
			System.out.println("dateString:" + dateString);
			
			for (int i = 0; i &lt; N; i++) {
				DateTime newDateTime = dateTime.plusDays(i);
				String newDateString = newDateTime.toString(dateTimeFormatter);
				
				System.out.println("newDateString:" + newDateString);
				context.write(new Text(newDateString), new FloatWritable(adjClose));
			}	
		}
	}

	public static class MovingAverageReducer extends Reducer&lt;Text, FloatWritable, Text, FloatWritable&gt; {
		
		public void reduce(
				Text key,
				Iterable&lt;FloatWritable&gt; values,
				Context context) throws IOException, InterruptedException {
			
			Float sum = 0.0f;
			int n = 0;
			for (FloatWritable value : values) {
				sum += value.get();
				n++;
			}
			
			Float movingAverage = sum / n;
			
			context.write(key, new FloatWritable(movingAverage));
		}
	}

	/**
	 * @param args
	 * @throws IOException 
	 * @throws InterruptedException 
	 * @throws ClassNotFoundException 
	 */
	public static void main(
			String[] args) throws IOException, ClassNotFoundException, InterruptedException {
		
		Configuration configuration = new Configuration();
		String[] otherArgs = new GenericOptionsParser(configuration, args).getRemainingArgs();
		
		String inputPath = otherArgs[0];
		String outputPath = otherArgs[1];
		
		Job job = new Job(configuration, "moving average");
		job.setJarByClass(Main.class);
		
		job.setMapperClass(MovingAverageMapper.class);
		job.setReducerClass(MovingAverageReducer.class);
		
		job.setMapOutputKeyClass(Text.class);
		job.setMapOutputValueClass(FloatWritable.class);
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(FloatWritable.class);
		
		FileInputFormat.addInputPath(job, new Path(inputPath));
		FileOutputFormat.setOutputPath(job, new Path(outputPath));
		
		System.exit(job.waitForCompletion(true) ? 0 : 1);
	}

}
</pre>