---
permalink: /news/
title: ""
author_profile: true
---

<!DOCTYPE html>
<html><head><meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

<link rel="stylesheet" href="./timeline_files/about.css">
<style>
.page { width:100%;background:#F0F0F0 url('img/dian2.png') repeat-x; padding-top:100px; }
</style>
<title>大事记 - 时间线</title>
<link rel="shortcut icon" type="image/x-icon" href="http://www.ibloger.net/favicon.ico">
</head>
<body data-new-gr-c-s-check-loaded="14.1006.0" data-gr-ext-installed="">
<div class="page">

<div class="box">
	<ul class="event_year">
		<li class="current"><label for="2021">2021</label></li>
		<li><label for="2020">2020</label></li>
		<li><label for="2019">2019</label></li>
		<li><label for="2018">2018</label></li>
		<li><label for="2017">2017</label></li>
		<li><label for="2016">2016</label></li>
		<li><label for="2015">2015</label></li>
	  <li><label for="2014">2014</label></li>
		<li><label for="2013">2013</label></li>
		<li><label for="2012">2012</label></li>
		<li><label for="2011">2011</label></li>
	</ul>

	<ul class="event_list">
		<div>
			<h3 id="2021">2021</h3>

			<li>
			<span>4月</span>
			<p><span>[北京] 和雯雯户外</span></p>
			</li>

			<li>
			<span>3月</span>
			<p><span>[武汉] 参加《奔跑吧！主播》</span></p>
			</li>

			<li>
			<span></span>
			<p><span>[郑州] 和Xx参加《龟兔赛跑》</span></p>
			</li>

			<li>
			<span></span>
			<p><span>[杭州] 和王灵佳带货</span></p>
			</li>

			<li>
			<span></span>
			<p><span>[武汉] 参加《助力乡村振兴，绘就壮美画卷》春分发布会</span></p>
			</li>

			<li>
			<span></span>
			<p><span>[南京] 和远洋，兮酱户外</span></p>
			</li>

			<li>
			<span></span>
			<p><span>[洛阳] 和小董户外</span></p>
			</li>

			<li>
			<span>2月</span>
			<p><span>[偃师] 兰氏牛肉酱“外交”</span></p>
			</li>

			<li>
			<span>1月</span>
			<p><span>[上海] 和远洋，囧哥带货</span></p>
			</li>

			<li>
			<span></span>
			<p><span>[上海] 参加《年度盛典》，获得“正能量之星称号”</span></p>
			</li>

			<li>
			<span></span>
			<p><span>[成都] 和芈芈户外</span></p>
			</li>

			<li>
			<span></span>
			<p><span>[成都] 和雪兔户外</span></p>
			</li>
		</div>

		<div>
			<h3 id="2020">2020</h3>

			<li>
			<span>12月</span>
			<p><span>[上海] 和<a href="https://www.douyu.com/3917746" target="_blank">大艺术嘉</a>户外</span></p>
			</li>

			<li>
			<span>12月</span>
			<p><span>[洛阳] 和<a href="https://www.douyu.com/3917746" target="_blank">鹅妹</a>户外</span></p>
			</li>

		</div>

	</ul>

	<div class="clearfix"></div>

	</div>

</div>

<script src="./timeline_files/jquery.min_v1.0.js" type="text/javascript"></script>
<script>
$(function(){
	$('label').click(function(){
		$('.event_year>li').removeClass('current');
		$(this).parent('li').addClass('current');
		var year = $(this).attr('for');
		$('#'+year).parent().prevAll('div').slideUp(800);
		$('#'+year).parent().slideDown(800).nextAll('div').slideDown(800);
	});
});
</script>

</body></html>
