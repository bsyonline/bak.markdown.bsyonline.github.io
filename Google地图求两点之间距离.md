---
title: Google地图求两点之间距离
toc: true
date: 2016-07-16 15:45:38
tags: gis
categories: technology

---

地球是一个近乎标准的椭球体，它的赤道半径为6378.140千米，极半径为6356.755千米，平均半径6371.004千米。如果我们假设地球是一个完美的球体，那么它的半径就是地球的平均半径，记为R。如果以0度经线为基准，那么根据地球表面任意两点的经纬度就可以计算出这两点间的地表距离（这里 忽略地球表面地形对计算带来的误差，仅仅是理论上的估算值）。设第一点A的经纬度为(LonA, LatA)，第二点B的经纬度为(LonB, LatB)，按照0度经线的基准，东经取经度的正值(Longitude)，西经取经度负值(-Longitude)，北纬取90-纬度值(90- Latitude)，南纬取90+纬度值(90+Latitude)，则经过上述处理过后的两点被计为(MLonA, MLatA)和(MLonB, MLatB)。那么根据三角推导，可以得到计算两点距离的如下公式：

	C = sin(MLatA)*sin(MLatB)*cos(MLonA-MLonB) + cos(MLatA)*cos(MLatB)

	Distance = R*Arccos(C)*Pi/180

这里，R和Distance单位是相同，如果是采用6371.004千米作为半径，那么Distance就是千米为单位，如果要使用其他单位，比如mile，还需要做单位换算，1千米=0.621371192mile
如果仅对经度作正负的处理，而不对纬度作90-Latitude(假设都是北半球，南半球只有澳洲具有应用意义)的处理，那么公式将是：


	C = sin(LatA)*sin(LatB) + cos(LatA)*cos(LatB)*cos(MLonA-MLonB)

	Distance = R*Arccos(C)*Pi/180

以上通过简单的三角变换就可以推出。

	var EARTH_RADIUS = 6378.137;
	function rad(d){
	    return d * Math.PI / 180.0;
	}

	function GetDistance(lat1, lng1, lat2, lng2){
	    var radLat1 = rad(lat1);
	    var radLat2 = rad(lat2);
	    var a = radLat1 - radLat2;
	    var b = rad(lng1) - rad(lng2);
	    var s = 2 * Math.asin(Math.sqrt(Math.pow(Math.sin(a/2),2) +
	Math.cos(radLat1)*Math.cos(radLat2)*Math.pow(Math.sin(b/2),2)));
	    s = s * EARTH_RADIUS;
	    s = Math.round(s * 10000) / 10000;
	    return s;
	}
