---
title: "明信片问候"
permalink: /postcards/
author_profile: true
---

<link rel="stylesheet" href="/assets/js/jvectormap/jquery-jvectormap-1.2.2.css">
<script src="/assets/js/jvectormap/jquery-1.9.1.min.js"></script>
<script src="/assets/js/jvectormap/jquery-jvectormap-1.2.2.min.js"></script>
<script src="/assets/js/jvectormap/jquery-jvectormap-world-mill.js"></script>

<div class="pc-map-wrap">
  <h2 class="pc-map-title">明信片地图</h2>
  <div class="pc-hint">点击国家/地区图标定位该区域 · 点击地图上的黄点查看明信片与说明 · 滚轮可缩放</div>
  <div class="pc-country-bar" id="pcCountryBar"></div>
  <div id="pcmap"></div>
</div>

<div id="pcPopup" hidden>
  <div class="pc-popup-card">
    <button type="button" class="pc-popup-close" id="pcPopupClose" aria-label="关闭">×</button>
    <div class="pc-popup-title" id="pcPopupTitle"></div>
    <div class="pc-popup-body" id="pcPopupBody"></div>
  </div>
</div>

<div class="postcards">

<h2 id="pc-text">图文说明（默认英语）</h2>

<ul>
{%- for cont in site.data.postcards -%}
<li>{{ cont.continent }}
  <ul>
  {%- for co in cont.countries -%}
  {%- if co.entries.size == 1 and co.entries[0].pre == co.name -%}
  {%- assign flat_city = co.entries[0] -%}
  {% include pc_city.html city=flat_city %}
  {%- else -%}
  <li>{{ co.name }}
    <ul>
    {%- for e in co.entries -%}
    {%- if e.cities -%}
    <li>{{ e.name }}
      <ul>
      {%- for c in e.cities -%}
      {% include pc_city.html city=c %}
      {%- endfor -%}
      </ul>
    </li>
    {%- else -%}
    {% include pc_city.html city=e %}
    {%- endif -%}
    {%- endfor -%}
    </ul>
  </li>
  {%- endif -%}
  {%- endfor -%}
  </ul>
</li>
{%- endfor -%}
</ul>

</div>

<script>
var PC_CITIES=[
{%- assign first = true -%}
{%- for cont in site.data.postcards -%}
{%- for co in cont.countries -%}
{%- for e in co.entries -%}
{%- if e.cities -%}
  {%- for c in e.cities -%}
  {%- unless first -%},{%- endunless -%}
{%- include pc_city_js.html city=c country=co.name continent=cont.continent -%}
  {%- assign first = false -%}
  {%- endfor -%}
{%- else -%}
  {%- unless first -%},{%- endunless -%}
{%- include pc_city_js.html city=e country=co.name continent=cont.continent -%}
  {%- assign first = false -%}
{%- endif -%}
{%- endfor -%}
{%- endfor -%}
{%- endfor -%}
];

(function () {
  // 国家按拼音 A-Z 排序
  var PC_CN_SORT = ['澳大利亚','白俄罗斯','比利时','波黑','波兰','德国','俄罗斯','芬兰','韩国','荷兰','加拿大','拉脱维亚','立陶宛','美国','日本','圣马力诺','斯洛文尼亚','泰国','乌克兰','西班牙','意大利','伊朗','印度','印度尼西亚','英国','中国'];
  function cnOrder(name) {
    for (var k = 0; k < PC_CN_SORT.length; k++) {
      if (PC_CN_SORT[k] === name) { return k; }
    }
    return 999;
  }
  var markers = [];
  var markerCity = [];
  var PC_BY_COUNTRY = {};
  var i, c;
  for (i = 0; i < PC_CITIES.length; i++) {
    c = PC_CITIES[i];
    if (!PC_BY_COUNTRY[c.country]) { PC_BY_COUNTRY[c.country] = []; }
    PC_BY_COUNTRY[c.country].push(c);
    if (c.lat === null || c.lng === null) { continue; }
    markerCity.push(c);
    markers.push({ latLng: [c.lat, c.lng], name: c.name });
  }

  function showCity(city) {
    var where = city.country + (city.region ? ' ' + city.region : '');
    if (city.name && city.name !== city.country) { where += ' ' + city.name; }
    document.getElementById('pcPopupTitle').innerHTML = where + (city.lang ? '（' + city.lang + '）' : '');
    var body = document.getElementById('pcPopupBody');
    body.className = 'pc-popup-body' + (city.images.length === 2 ? ' pc-multi2' : (city.images.length >= 3 ? ' pc-multi3' : ''));
    var html = '';
    for (var j = 0; j < city.images.length; j++) {
      html += '<figure><a href="' + city.images[j] + '" target="_blank" rel="noopener"><img src="' + city.images[j] + '" alt="明信片" loading="lazy"></a>';
      var note = city.notes[j] || '';
      note = note.replace(/^[\s，,、；;：:。]+/, '').replace(/[\s，,、；;：:。]+$/, '');
      if (note) { html += '<figcaption>' + note + '</figcaption>'; }
      html += '</figure>';
    }
    body.innerHTML = html;
    document.getElementById('pcPopup').hidden = false;
  }
  function closePopup() { document.getElementById('pcPopup').hidden = true; }

  // 建图前处理地图数据:
  // 1) 台湾路径并入中国 → 同一区域、同色、悬停一起高亮
  // 2) 注入圣马力诺独立区域(世界地图数据缺少这个微型国家; 坐标按 Miller 投影计算)
  try {
    var jm = window.jvm;
    var wm = jm && jm.WorldMap && jm.WorldMap.maps && jm.WorldMap.maps['world_mill'];
    if (wm && wm.paths) {
      if (wm.paths['CN'] && wm.paths['TW']) {
        wm.paths['CN'].path = wm.paths['CN'].path + ' ' + wm.paths['TW'].path;
        delete wm.paths['TW'];
      }
      if (!wm.paths['SM']) {
        wm.paths['SM'] = { name: '圣马力诺', path: 'M453.45,167.23L453.27,167.88L452.80,168.35L452.15,168.53L451.50,168.35L451.02,167.88L450.85,167.23L451.02,166.58L451.50,166.10L452.15,165.93L452.80,166.10L453.27,166.58Z' };
      }
    }
  } catch (err) {}

  $('#pcmap').vectorMap({
    map: 'world_mill',
    backgroundColor: '#353535',
    zoomOnScroll: true,
    zoomButtons: false,
    markerStyle: {
      initial: { fill: '#F8E23B', stroke: '#383f47', r: 5 },
      hover: { fill: '#ffffff', stroke: '#383f47' }
    },
    markers: markers,
    onMarkerClick: function (e, code) {
      if (e && e.preventDefault) { e.preventDefault(); }
      showCity(markerCity[+code]);
    }
  });
  var mapObj = $('#pcmap').vectorMap('get', 'mapObject');

  // 圣马力诺小区域加描边, 避免与意大利同色不可见
  try {
    var smEl = mapObj.regions && mapObj.regions['SM'] && mapObj.regions['SM'].element;
    if (smEl) {
      smEl.setAttribute('stroke', '#474749');
      smEl.setAttribute('stroke-width', '0.4');
    }
  } catch (err) {}

  // 基于城市坐标 + Miller 投影计算聚焦(不依赖国家的SVG包围盒——美国/俄罗斯等跨国界路径的包围盒几乎覆盖全图)
  var MAP_R = 6381372, MAP_CM = 11.5;
  var MAP_X0 = -20004297.151525836, MAP_X1 = 20026572.394749384;
  var MAP_Y0 = -12671671.123330014, MAP_Y1 = 6930392.025135122;
  function latLngToFrac(lat, lng) {
    var x = MAP_R * (lng - MAP_CM) * Math.PI / 180;
    var y = -MAP_R * Math.log(Math.tan((45 + 0.4 * lat) * Math.PI / 180)) / 0.8;
    return { fx: (x - MAP_X0) / (MAP_X1 - MAP_X0), fy: (y - MAP_Y0) / (MAP_Y1 - MAP_Y0) };
  }
  function focusCountry(name) {
    var arr = PC_BY_COUNTRY[name];
    if (!arr) { return; }
    var minX = 9, maxX = -9, minY = 9, maxY = -9, n = 0, k;
    for (k = 0; k < arr.length; k++) {
      if (arr[k].lat === null || arr[k].lng === null) { continue; }
      var f = latLngToFrac(arr[k].lat, arr[k].lng);
      if (f.fx < minX) { minX = f.fx; }
      if (f.fx > maxX) { maxX = f.fx; }
      if (f.fy < minY) { minY = f.fy; }
      if (f.fy > maxY) { maxY = f.fy; }
      n++;
    }
    if (!n) { return; }
    var cx = (minX + maxX) / 2, cy = (minY + maxY) / 2;
    var fw = maxX - minX, fh = maxY - minY;
    var scale = 18;
    if (n > 1 && fw > 0.004 && fh > 0.004) {
      scale = Math.min(0.85 / fw, 0.55 / fh);
      if (scale < 1.6) { scale = 1.6; }
      if (scale > 30) { scale = 30; }
    }
    try { mapObj.setFocus(scale, cx, cy); } catch (err) {}
  }

  var byCont = {};
  for (i = 0; i < PC_CITIES.length; i++) {
    c = PC_CITIES[i];
    if (!byCont[c.continent]) { byCont[c.continent] = {}; }
    if (!byCont[c.continent][c.country]) { byCont[c.continent][c.country] = []; }
    byCont[c.continent][c.country].push(c);
  }
  var bar = document.getElementById('pcCountryBar');
  for (var contName in byCont) {
    if (!byCont.hasOwnProperty(contName)) { continue; }
    var block = document.createElement('div');
    block.className = 'pc-continent';
    var lab = document.createElement('div');
    lab.className = 'pc-continent-name';
    lab.innerHTML = contName;
    block.appendChild(lab);
    var row = document.createElement('div');
    row.className = 'pc-continent-countries';
    var countries = byCont[contName];
    var names = [];
    for (var ck in countries) {
      if (countries.hasOwnProperty(ck)) { names.push(ck); }
    }
    names.sort(function (a, b) { return cnOrder(a) - cnOrder(b); });
    for (var ni = 0; ni < names.length; ni++) {
      var cn = names[ni];
      var arr = countries[cn];
      var icon = '';
      for (i = 0; i < arr.length; i++) {
        if (arr[i].images.length) { icon = arr[i].images[0]; break; }
      }
      var btn = document.createElement('button');
      btn.type = 'button';
      btn.className = 'pc-country';
      btn.innerHTML = (icon ? '<img src="' + icon + '" alt="">' : '') + '<span>' + cn + '</span>';
      (function (name) {
        btn.addEventListener('click', function () { focusCountry(name); });
      })(cn);
      row.appendChild(btn);
    }
    block.appendChild(row);
    bar.appendChild(block);
  }
  var reset = document.createElement('button');
  reset.type = 'button';
  reset.className = 'pc-country pc-reset';
  reset.innerHTML = '<span>重置视图</span>';
  reset.addEventListener('click', function () {
    try { mapObj.setFocus(1, 0, 0); } catch (err) {}
  });
  bar.appendChild(reset);

  document.getElementById('pcPopupClose').addEventListener('click', closePopup);
  document.getElementById('pcPopup').addEventListener('click', function (e) {
    if (e.target === this) { closePopup(); }
  });
})();
</script>
