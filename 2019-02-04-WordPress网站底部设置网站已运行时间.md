### 给WordPress网站底部设置已运行时间：
#### 方法一：在主题设置中找到个性化页脚内容设置框，填写如下代码即可：
```html
网站已运行:<span id="run_time" style="color: black;"></span>
<script>
function runTime() {
    var d = new Date(), str = '';
    BirthDay = new Date("2018-12-31");
    today = new Date();
    timeold = (today.getTime() - BirthDay.getTime());
    sectimeold = timeold / 1000
    secondsold = Math.floor(sectimeold);
    msPerDay = 24 * 60 * 60 * 1000
    msPerYear = 365 * 24 * 60 * 60 * 1000
    e_daysold = timeold / msPerDay
    e_yearsold = timeold / msPerYear
    daysold = Math.floor(e_daysold);
    yearsold = Math.floor(e_yearsold);
    //str = yearsold + "年";
    str += daysold + "天";
    str += d.getHours() + '时';
    str += d.getMinutes() + '分';
    str += d.getSeconds() + '秒';
    return str;
}

setInterval(function () {
    $('#run_time').html(runTime())
}, 1000);
</script>
```
#### 方法二：在footer.php代码中添加如下代码即可：
```html
网站已运行:<span id="run_time" style="color: black;"></span>
<script>
function runTime() {
    var d = new Date(), str = ''; 
    BirthDay = new Date("2018-12-31");
    today = new Date();
    timeold = (today.getTime() - BirthDay.getTime());
    sectimeold = timeold / 1000
    secondsold = Math.floor(sectimeold);
    msPerDay = 24 * 60 * 60 * 1000
    msPerYear = 365 * 24 * 60 * 60 * 1000
    e_daysold = timeold / msPerDay
    e_yearsold = timeold / msPerYear
    daysold = Math.floor(e_daysold);
    yearsold = Math.floor(e_yearsold);
    //str = yearsold + "年";
    str += daysold + "天";
    str += d.getHours() + '时';
    str += d.getMinutes() + '分';
    str += d.getSeconds() + '秒';
    return str;
}

setInterval(function () {
    $('#run_time').html(runTime())
}, 1000);
</script>
```
