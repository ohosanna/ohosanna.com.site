---
layout: post
title: 获取实时天气信息的Perl脚本
created: 2010-07-17 16:50:50.000000000 +08:00
tags: perl
---

前段时间捣鼓Perl的时候，根据Yahoo!提供的天气状况API写了一个脚本，用来分析并获取实时天气状况的信息。现在这个脚本已经被我用在了FVWM桌面的天气状况显示上，另外，使用第三方的飞信API，还可以把信息发到手机上，呵呵，还是蛮实用的。

```perl
#!/usr/bin/perl
###################
#根据http://developer.yahoo.com/weather/提供的API写的获取天气信息的脚本
#作者：一介农夫
###################
use strict;
use Encode;
use LWP::Simple;

my $wid = "2161664"; #中山，各个具体的wid可以到http://weather.yahoo.com/去搜索
my $url = "http://weather.yahooapis.com/forecastrss?w=$wid&amp;u=c";
my $data = get($url);
$data =~ s/<yweather\:forecast\s+(.*?)\>//g; #去掉后面重复的两行信息，暂时未知如何处理。
my $use_data = "";
my @temp_data = split /\n/, $data;
foreach my $temp_data (@temp_data) { 
    if ($temp_data =~ m/<yweather\:\w+\s+(.*?)\>/ig) {
	$use_data .= " ".$1."\n";
    }
}

my %info ="";
my %text = (
    "0" => "龙卷风",
    "1" => "热带风暴",
    "2" => "飓风",
    "3" => "雷暴",
    "4" => "雷暴",
    "5" => "雨夹雪",
    "6" => "雨夹雪",
    "7" => "雨夹雪",
    "8" => "冻雨",
    "9" => "冻雨",
    "10" => "冻雨",
    "11" => "阵雨",
    "12" => "阵雨",
    "13" => "小雪",
    "14" => "阵雪",
    "15" => "风吹雪",
    "16" => "雪",
    "17" => "冰雹",
    "18" => "冻雨",
    "19" => "沙尘",
    "20" => "模糊",
    "21" => "薄雾",
    "22" => "污染",
    "23" => "坏天气",
    "24" => "有风",
    "25" => "冷",
    "26" => "阴天",
    "27" => "多云",
    "28" => "多云",
    "29" => "少云",
    "30" => "少云",
    "31" => "晴",
    "32" => "睛",
    "33" => "睛",
    "34" => "睛",
    "35" => "雨夹冰雹",
    "36" => "热",
    "37" => "局部雷暴",
    "38" => "分散雷雨",
    "39" => "分散雷雨",
    "40" =>"零星阵雨",
    "41" => "大雪",
    "42" => "零星阵雪",
    "43" => "大雪",
    "44" => "多云",
    "45" => "雷阵雨",
    "46" => "阵雨",
    "47" => "雷阵雨",
    "3200" => "未知",
);
my @condition = split /\"\s+/, $use_data;
foreach my $condition (@condition) {
    if ($condition =~ m/^(\w+)\s*=\s*\"(.*?)\s*$/) {
	$info{"$1"} = "$2";
    }
}

my $message = decode ("utf8","中山天气状况：$text{$info{code}}，温度：$info{temp}C，湿度：$info{humidity}％，能见度：$info{visibility}KM，气压：$info{pressure}MB，日出时间：$info{sunrise}，日落时间$info{sunset}。$info{date}");
$message = encode("gb2312","$message"); #中文编码处理

my $send_url = "http://sms.api.bz/fetion.php?username=手机号码&password=飞信密码&sendto=要发送的手机&message=$message";
#print $message;
my $send = get($send_url);
```
