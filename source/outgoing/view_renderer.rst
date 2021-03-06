#############
View Renderer
#############

.. contents::
    :local:
    :depth: 2

The ``view()`` function is a convenience function that grabs an instance of the
``renderer`` service, sets the data, and renders the view. While this is often
exactly what you want, you may find times where you want to work with it more directly.
In that case you can access the View service directly
``view()`` 함수는 ``renderer`` 서비스의 인스턴스를 생성하고 데이터를 설정하고 뷰를 렌더링 하는 편리한 함수입니다.
이것은 종종 정확히 원하는 동안, 당신은 당신이 그것을 더 직접적으로 사용하기를 원하는 곳을 찾을 것입니다. 
이 경우 View 서비스에 직접 액세스 할 수 있습니다.

::

	$view = \Config\Services::renderer();

Alternately, if you are not using the ``View`` class as your default renderer, you
can instantiate it directly
또는 View클래스를 기본 렌더러로 사용하지 않는 경우 클래스를 직접 인스턴스화 할 수 있습니다.

::

	$view = new \CodeIgniter\View\View();

.. important:: You should create services only within controllers. If you need
	access to the View class from a library, you should set that as a dependency
	in your library's constructor.
	컨트롤러 내에서만 서비스를 생성해야합니다. 라이브러리에서 View 클래스에 액세스해야하는 경우 라이브러리 생성자에서 종속성으로 설정해야합니다.

Then you can use any of the three standard methods that it provides:
**render(viewpath, options, save)**, **setVar(name, value, context)** and **setData(data, context)**.
그런 다음 **render(viewpath, options, save)** , **setVar(name, value, context)** 및 **setData(data, context)**
의 세 가지 표준 메서드 중 하나를 사용할 수 있습니다 .

What It Does
============

The ``View`` class processes conventional HTML/PHP scripts stored in the application's view path,
after extracting view parameters into PHP variables, accessible inside the scripts.
This means that your view parameter names need to be legal PHP variable names.
``View`` 클래스는 뷰 매개 변수를 PHP 변수로 추출한 후 응용 프로그램의 뷰 경로에 저장된 
HTML/PHP 스크립트에서 처리합니다(스크립트 내에서 액세스 가능). 즉, 뷰 매개 변수 이름은
합법적인 PHP 변수 이름이어야 합니다.

The View class uses an associative array internally, to accumulate view parameters
until you call its ``render()``. This means that your parameter (or variable) names
need to be unique, or a later variable setting will over-ride an earlier one.
View 클래스는 내부적으로 연관 배열을 사용하여 뷰 매개 변수를 ``render()`` 호출 할 때까지 누적합니다.
매개 변수(또는 변수) 이름이 고유하지 않을경우 같은 이름으로 변수 설정을 하게되면 이전 매개 변수를 오버라이드합니다.

This also impacts escaping parameter values for different contexts inside your
script. You will have to give each escaped value a unique parameter name.
이것은 또한 스크립트 내의 다른 컨텍스트에 대한 이스케이프 매개 변수 값에 영향을줍니다.
이스케이프된 각 값에 고유 한 매개 변수 이름을 지정해야합니다.

No special meaning is attached to parameters whose value is an array. It is up
to you to process the array appropriately in your PHP code.
값이 배열 인 매개 변수에는 특별한 의미가 없습니다.
PHP 코드에서 배열을 적절히 처리하는 것은 당신에게 달려 있습니다.

Method Chaining
===============

The ``setVar()`` and ``setData()`` methods are chainable, allowing you to combine a
number of different calls together in a chain
SETVAR () 및 사항 setData () 메소드는 체인에 함께 다른 통화의 수를 결합 할 수 있도록 체인 방식입니다

::

	$view->setVar('one', $one)
	     ->setVar('two', $two)
	     ->render('myView');

Escaping Data
=============

When you pass data to the ``setVar()`` and ``setData()`` functions you have the option to escape the data to protect
against cross-site scripting attacks. As the last parameter in either method, you can pass the desired context to
escape the data for. See below for context descriptions.
setVar()및 setData()기능에 데이터를 전달할 때 교차 사이트 스크립팅 공격으로부터 보호하기 위해 데이터를 이스케이프하도록 선택할 수 있습니다. 두 방법 중 마지막 매개 변수로 원하는 컨텍스트를 전달하여 데이터를 이스케이프 처리 할 수 있습니다. 문맥에 대한 설명은 아래를 참조하십시오.

If you don't want the data to be escaped, you can pass ``null`` or ``raw`` as the final parameter to each function
데이터를 이스케이프하지 않으려면 null 또는 raw 를 각 함수의 최종 매개 변수로 전달할 수 있습니다.

::

	$view->setVar('one', $one, 'raw');

If you choose not to escape data, or you are passing in an object instance, you can manually escape the data within
the view with the ``esc()`` function. The first parameter is the string to escape. The second parameter is the
context to escape the data for (see below)
데이터를 이스케이프하지 않거나 객체 인스턴스를 전달하려는 경우 esc()함수를 사용 하여 뷰 내에서 수동으로 데이터를 이스케이프 처리 할 수 있습니다 . 첫 번째 매개 변수는 이스케이프 할 문자열입니다. 두 번째 매개 변수는 데이터를 이스케이프 처리하는 컨텍스트입니다 (아래 참조).

::

	<?= \esc($object->getStat()) ?>

Escaping Contexts
-----------------

By default, the ``esc()`` and, in turn, the ``setVar()`` and ``setData()`` functions assume that the data you want to
escape is intended to be used within standard HTML. However, if the data is intended for use in Javascript, CSS,
or in an href attribute, you would need different escaping rules to be effective. You can pass in the name of the
context as the second parameter. Valid contexts are 'html', 'js', 'css', 'url', and 'attr'
기본적으로는 ``esc()`` 와, ``setVar()` 그리고 ``setData()`` 기능을 이스케이프할 데이터가 표준 HTML 내에서 사용하도록되어 있다고 
가정합니다. 그러나 데이터가 Javascript, CSS 또는 href 속성에서 사용하기 위한 것이면 다른 이스케이프 규칙이 효과적이어야합니다.
두 번째 매개 변수로 컨텍스트의 이름을 전달할 수 있습니다. 유효한 컨텍스트는 'html', 'js', 'css', 'url' 및 'attr'입니다.

::

	<a href="<?= esc($url, 'url') ?>" data-foo="<?= esc($bar, 'attr') ?>">Some Link</a>

	<script>
		var siteName = '<?= esc($siteName, 'js') ?>';
	</script>

	<style>
		body {
			background-color: <?= esc('bgColor', 'css') ?>
		}
	</style>

View Renderer Options
=====================

``render()`` 또는 ``renderString()`` 메소드를 통해 여러 옵션을 전달할 수 있습니다. :

-   ``cache`` - 뷰의 결과를 저장하는 시간(초 단위). renderString() 에서는 무시됩니다.
-   ``cache_name`` - 캐시 된 뷰 결과를 저장 / 검색하는 데 사용되는 ID. 기본값은 viewpath입니다. renderString() 에서는 무시됩니다.
-   ``saveData`` - true 인경우 후속 호출에 대해 뷰 데이터 매개 변수를 유지합니다.

Class Reference
===============

.. php:class:: CodeIgniter\\View\\View

	.. php:method:: render($view[, $options[, $saveData=false]]])
                :noindex:

		:param  string  $view: 뷰 소스의 파일 이름
		:param  array   $options: 옵션 배열, 키/값 쌍
		:param  boolean $saveData: true이면 다음 호출을 위해 사용할 데이터를 저장하고, false이면 뷰를 렌더링 한 후 데이터를 삭제합니다.
		:returns: 선택한 View의 렌더링 된 텍스트
		:rtype: string

		파일 이름과 이미 설정된 모든 데이터를 기반으로 출력을 작성합니다. ::

			echo $view->render('myview');

	.. php:method:: renderString($view[, $options[, $saveData=false]]])
                :noindex:

		:param  string  $view: Contents of the view to render, for instance content retrieved from a database
		:param  array   $options: Array of options, as key/value pairs
		:param  boolean $saveData: If true, will save data for use with any other calls, if false, will clean the data after rendering the view.
		:returns: The rendered text for the chosen view
		:rtype: string

		Builds the output based upon a view fragment and any data that has already been set::

			echo $view->renderString('<div>My Sharona</div>');

		This could be used for displaying content that might have been stored in a database,
		but you need to be aware that this is a potential security vulnerability,
		and that you **must** validate any such data, and probably escape it
		appropriately!

	.. php:method:: setData([$data[, $context=null]])
                :noindex:

		:param  array   $data: Array of view data strings, as key/value pairs
		:param  string  $context: The context to use for data escaping.
		:returns: The Renderer, for method chaining
		:rtype: CodeIgniter\\View\\RendererInterface.

		Sets several pieces of view data at once::

			$view->setData(['name'=>'George', 'position'=>'Boss']);

		Supported escape contexts: html, css, js, url, or attr or raw.
		If 'raw', no escaping will happen.

		Each call adds to the array of data that the object is accumulating,
		until the view is rendered.

	.. php:method:: setVar($name[, $value=null[, $context=null]])
                :noindex:

		:param  string  $name: Name of the view data variable
		:param  mixed   $value: The value of this view data
		:param  string  $context: The context to use for data escaping.
		:returns: The Renderer, for method chaining
		:rtype: CodeIgniter\\View\\RendererInterface.

		Sets a single piece of view data::

			$view->setVar('name','Joe','html');

		Supported escape contexts: html, css, js, url, attr or raw.
		If 'raw', no escaping will happen.

		If you use the a view data variable that you have previously used
		for this object, the new value will replace the existing one.
