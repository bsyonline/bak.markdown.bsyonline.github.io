blackberry：监听浏览器
=
---
title: 单机搭建elk+logback
toc: true
date: 2016-07-16 15:53:17
tags:
categories:
---

	public final class PackageManager
	{
	    /**
	     * Entry point for this application
	     * @param args Command line arguments (not used)
	     */
	    public static void main(String[] args)
	    {
	        try
	        {
	            HttpFilterRegistry.registerFilter("com.unionpay.m.p", "com.rim.samples.device.httpfilterdemo.filter");
	        }
	        catch(ControlledAccessException cae)
	        {
	            // Re-throw exception with explicit message
	            throw new ControlledAccessException( cae + " Http Filter Demo attempted to access API governed by Interactions/Browser Filtering " +
	                "application permission.  Please set this permission to 'allow' under Options/Security Options/Application Permissions");
	        }
	    }
	}
