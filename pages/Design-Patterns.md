---
layout: page
title: 디자인 패턴 
---

# 디자인 패턴

여러분의 웹 어플리케이션 코드와 프로젝트를 구조화하는 데에는 여러가지 방법이 많습니다. 여러분은 어플리케이션의 아키텍처에 최대한으로 신경을 쓸지, 혹은 최소한으로 신경을 쓸 것인지 결정할 수도 있습니다. 보통은 잘 알려진 일반적인 패턴을 따르는 것이 좋습니다. 프로젝트를 관리하기에도 좋고, 다른 개발자들이 이해하기도 좋으니까요.

* [Architectural pattern on Wikipedia](https://en.wikipedia.org/wiki/Architectural_pattern)
* [Software design pattern on Wikipedia](https://en.wikipedia.org/wiki/Software_design_pattern)

## 팩토리

팩토리 패턴(factory pattern)은 아마 디자인 패턴 중에서도 가장 널리 사용되고 있을 것 같습니다. 클래스 하나가 여러분이 원하는 클래스 개체를 생성해주는 패턴입니다. 아래에서 예제를 보시죠.

{% highlight php %}
<?php
class Automobile
{
    private $vehicle_make;
    private $vehicle_model;

    public function __construct($make, $model)
    {
        $this->vehicle_make = $make;
        $this->vehicle_model = $model;
    }

    public function get_make_and_model()
    {
        return $this->vehicle_make . ' ' . $this->vehicle_model;
    }
}

class AutomobileFactory
{
    public static function create($make, $model)
    {
        return new Automobile($make, $model);
    }
}

// Automobile 개체를 생성하는 팩토리 사용
$veyron = AutomobileFactory::create('Bugatti', 'Veyron');

print_r($veyron->get_make_and_model()); // "Bugatti Veyron"을 출력
{% endhighlight %}

이 코드에서 Automobile 개체를 생성할 때 팩토리를 사용하고 있습니다. 이런 식으로 코드를 작성하면 두 가지 정도 이점이 있습니다. 하나는 Automobile 클래스 이름을 바꾸거나, 변경이 생기거나, 다른 클래스로 대체해도 Automobile 클래스를 사용하는 모든 곳의 코드를 고치지 않고 팩토리 클래스에 있는 코드만 수정하면 된다는 것입니다. 두번째 이점은 생성하는 과정이 복잡한 클래스 개체라고 해도 그런 코드가 팩토리에만 있고 그 클래스를 생성하는 모든 곳에 반복적으로 복잡한 코드를 넣지 않아도 된다는 것입니다.

항상 팩토리 패턴을 사용해야 하는 것은 아닙니다. 바로 위 예제 코드 정도라면 팩토리 패턴을 사용했기 때문에 오히려 불필요하게 더 복잡해졌다고 할 수 있습니다. 하지만 실제 프로젝트를 수행할 때에는 팩토리를 사용해서 도움을 얻을 수 있는 경우가 많을 것입니다.

* [Factory pattern on Wikipedia](https://en.wikipedia.org/wiki/Factory_pattern)

## 싱글턴

웹 어플리케이션을 설계할 때 특정 클래스의 경우에는 오직 하나의 인스턴스만이 존재하여 모든 곳에서 그 인스턴스만을 사용해야 한다는 결정이 의미있는 경우가 있습니다. 싱글턴 패턴(singleton pattern)을 사용하여 그런 클래스를 만들어 냅니다.

{% highlight php %}
<?php
class Singleton
{
    /**
     * 이 클래스의 *싱글턴* 인스턴스를 리턴한다.
     *
     * @staticvar Singleton $instance 이 클래스의 *싱글턴* 인스턴스
     *
     * @return Singleton *싱글턴* 인스턴스.
     */
    public static function getInstance()
    {
        static $instance = null;
        if (null === $instance) {
            $instance = new static;
        }

        return $instance;
    }

    /**
     * 이 클래스는 *싱글턴*으로 사용할 것이므로 이 클래스 외부에서
     * 생성하는 것을 금지하기 위해 생성자를 protected 로 제한한다.
     */
    protected function __construct()
    {
    }

    /**
     * *싱글턴* 인스턴스를 복제할 수 없도록 복제 메소드를 private
     * 으로 제한한다.
     *
     * @return void
     */
    private function __clone()
    {
    }

    /**
     * *싱글턴* 인스턴스를 unserialize 하지 못하게 private 으로 제한한다.
     *
     * @return void
     */
    private function __wakeup()
    {
    }
}

class SingletonChild extends Singleton
{
}

$obj = Singleton::getInstance();
\var_dump($obj === Singleton::getInstance());             // bool(true)

$anotherObj = SingletonChild::getInstance();
\var_dump($anotherObj === Singleton::getInstance());      // bool(false)

\var_dump($anotherObj === SingletonChild::getInstance()); // bool(true)
{% endhighlight %}

이 코드에서는 [*정적(static)* 변수](http://php.net/language.variables.scope#language.variables.scope.static)와 정적 생성 메소드(`getInstance()`)를 사용한 싱글턴 구현을 보여주고 있습니다. 아래 내용을 유념하세요.
Note the following:

* 생성자 [`__construct`](http://php.net/language.oop5.decon#object.construct)는 `new` 연산자를 사용해서 다른 곳에서 함부로 생성할 수 없도록 protected 로 제한되어 있습니다.
* Magic Method [`__clone`](http://php.net/language.oop5.cloning#object.clone)은 [`clone`](http://php.net/language.oop5.cloning) 연산자를 사용해서 복제할 수 없도록 private 으로 제한되어 있습니다.
* Magic Method [`__wakeup`](http://php.net/language.oop5.magic#object.wakeup)은 전역 함수 [`\unserialize()`](http://php.net/function.unserialize)를 이용해서 unserialize 할 수 없도록 private 으로 제한되어 있습니다.
* 새 인스턴스 생성 시에는 정적 메소드인 `getInstance()` 내에서 [지연된 정적 바인딩](http://php.net/language.oop5.late-static-bindings)을 통해서 생성됩니다. `static` 키워드가 사용되고 있는데요, 지연된 정적 바인딩을 사용함으로써 `Singleton` 클래스를 상속해서 싱글턴 패턴을 사용하는 자식 클래스들을 만들 수 있게 됩니다.

웹 어플리케이션의 HTTP 요청 처리 사이클에서 특정 클래스의 인스턴스가 단 하나만 존재해야 한다는 것을 명확히 하고 싶을 때 싱글턴 패턴을 사용하면 됩니다. Configuration 클래스 같은 전역 개체나 이벤트 큐 같은 공유 리소스의 경우 통상적으로 그러한 성격을 띄는 경우가 많습니다.

싱글턴 패턴을 사용할 때 주의해야 할 것은, 패턴의 특성상 어플리케이션 전체 범위에 영향을 주는 일종의 상태 정보가 생긴다는 것입니다. 이런 특성 때문에 테스트 가능성을 떨어뜨립니다. 대부분의 경우에는 싱글턴 클래스 대신 의존성 주입(Dependency Injection)을 사용할 수 있으므로 가능하다면 싱글턴을 피하는 편이 좋을 것입니다. 의존성 주입을 사용하게 되면 공유되는 리소스를 사용하는 클래스라고 해도 구체적인 싱글턴 클래스의 구현에 의존적이지 않게 되므로 설계적으로 더 낫습니다.

* [Singleton pattern on Wikipedia](https://en.wikipedia.org/wiki/Singleton_pattern)

## Front Controller

프론트 컨트롤러(front controller) 패턴은 웹 어플리케이션으로 오는 모든 리소스를 처리해주는 하나의 진입점(예를들면 index.php)을 두는 패턴입니다. 컨트롤러에서는 모든 의존 관계를 로딩하고, HTTP 요청을 처리한 후 응답을 보내주는 것 까지의 과정을 책임집니다. 프론트 컨트롤러 패턴을 사용하면 코드가 잘 모듈화되는 경향이 있고, 모든 요청에 대해서 항상 수행되어야 하는 작업(입력 값에서 위험한 데이터를 걸러내는 등의 작업)을 수행시킬 수 있는 중심점으로서 컨트롤러를 생각할 수 다는 이점이 있습니다.

* [Front Controller pattern on Wikipedia](https://en.wikipedia.org/wiki/Front_Controller_pattern)

## Model-View-Controller

모델-뷰-컨트롤러(MVC) 패턴과 이 패턴의 변형이라고 할 수 있는 HMVC나 MVVM 패턴은 어플리케이션 코드들을 특정 역할을 수행하는 논리적인 단위로 구분해줍니다. '모델'은 데이터에 접근하는 레이어로서 데이터소스에서 데이터를 가져와서, 어플리케이션에서 사용할 수 있는 형태로 만든 데이터를 제공해 줍니다. '컨트롤러'는 HTTP 요청을 받고, 모델로부터 얻은 데이터를 처리하고, '뷰'를 로드하여 응답을 보냅니다. '뷰'는 웹 브라우저에게 응답으로 제공되는 템플릿(마크업, XML, JSON 등)을 표시합니다.

MVC는 인기있는 [PHP 프레임워크들](https://github.com/codeguy/php-the-right-way/wiki/Frameworks)에서 가장 일반적으로 사용되는 아키텍처 패턴입니다.

MVC 패턴, 그리고 그 패턴과 관련있는 패턴에 대해서는 아래 링크를 통해서 더 알아볼 수 있습니다.

* [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93View%E2%80%93Controller)
* [HMVC](https://en.wikipedia.org/wiki/Hierarchical_model%E2%80%93view%E2%80%93controller)
* [MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel)
