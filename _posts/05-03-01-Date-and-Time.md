---
title:   날짜와 시간
isChild: true
---

## 날짜와 시간 {#date_and_time_title}

날짜와 시간을 읽고, 쓰고, 비교하고, 계산할 때 사용할 수 있는 DateTime 이라는 클래스가 있습니다. PHP에는 DateTime 외에도
날짜와 시간을 다루는 많은 함수들이 있지만 DateTime 클래스는 일반적인 사용 시나리오에 맞는 괜찮은 개체지향적인 인터페이스를 
제공합니다. 시간대(time zone)을 다루는 기능도 갖추고 있지만 이 사이트의 짧은 소개에서 다룰만한 범위는 아닙니다.

DateTime 클래스를 사용하려면 문자열 형태로 표현한 날짜와 시간 정보를 `createFromFormat()` 팩토리 메소드를 사용해서
개체 인스턴스로 변경하거나, `new \DateTime` 생성자로 현재 날짜와 시간 정보를 얻으면 됩니다. 그리고 DateTime 개체를 다시
문자열로 변경하려면 `format()` 메소드를 사용하면 됩니다.
{% highlight php %}
<?php
$raw = '22. 11. 1968';
$start = \DateTime::createFromFormat('d. m. Y', $raw);

echo 'Start date: ' . $start->format('m/d/Y') . "\n";
{% endhighlight %}

DateInterval 클래스를 이용해서 DateTime을 이용한 계산이 가능합니다. DateTime 클래스에는 DateInterval을 인자로 받는 
`add()` 나 `sub()` 같은 메소드가 있습니다. 하루의 길이가 매일 동일할 것이라고 가정하고 코드를 작성하지 않아야 합니다.
일광절약시간제(daylight saving time)나 시간대(timezon)의 변경에 따라서 하루의 길이는 달라질 수가 있기 때문입니다.
그러므로, 날짜 계산을 할 때 초단위의 숫자를 사용하는 대신 DateInterval을 사용하세요. 날짜의 차이를 계산하려면 `diff()` 메소드를
사용하면 됩니다. `diff()` 메소드는 DateInterval을 리턴하는데, 표시하기에도 아주 쉽습니다.
{% highlight php %}
<?php
// $start를 복제한 다음 한 달하고 6일을 더한다.
$end = clone $start;
$end->add(new \DateInterval('P1M6D'));

$diff = $end->diff($start);
echo 'Difference: ' . $diff->format('%m month, %d days (total: %a days)') . "\n";
// Difference: 1 month, 6 days (total: 37 days)
{% endhighlight %}

DateTime 개체를 일반적인 비교 연산자를 이용해서 비교하는 것이 가능합니다.
{% highlight php %}
<?php
if ($start < $end) {
    echo "Start is before end!\n";
}
{% endhighlight %}

DateTime에 대한 마지막 예제는 DatePeriod 클래스에 대한 것입니다. 이 클래스는 반복적으로 발생하는 사건을 다루는데 사용됩니다.
start와 end라는 두 개의 DateTime 개체와 시간 간격을 나타내는 DateInterval 개체 하나를 받아서, 
지정된 기간에 발생하는 모든 사건을 리턴해줍니다.
{% highlight php %}
<?php
// $start 와 $end 사이의 모든 목요일을 얻는다.
$periodInterval = \DateInterval::createFromDateString('first thursday');
$periodIterator = new \DatePeriod($start, $periodInterval, $end, \DatePeriod::EXCLUDE_START_DATE);
foreach ($periodIterator as $date) {
    // 지정된 기간 안에 있는 모든 날짜를 출력한다.
    echo $date->format('m/d/Y') . ' ';
}
{% endhighlight %}

* [Read about DateTime][datetime]
* [Read about date formatting][dateformat] (날짜를 문자열로 변경할 때 사용할 수 있는 옵션 패턴들)

[datetime]: http://www.php.net/manual/book.datetime.php
[dateformat]: http://www.php.net/manual/function.date.php
