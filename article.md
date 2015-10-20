<blockquote>
<p class="md-focus"><span class="md-expand">你用C写代码的时候，一不小心就会搬起石头砸自己的脚；用C++会稍微好一些，但万一你砸到了，那么整条腿就废了。</span></p>
——C++之父，Bjarne Stroustrup</blockquote>
<p class="">C++是一门非常强大而复杂的语言，而<strong>模板</strong><span class="">（template）则是其“主打功能”之一。可以说，没有了模板，C++就不会是C++，它不会有现在这样的灵活性和可拓展性，也不会像现在这样因为过于复杂而受人诟病。</span></p>
本文将介绍C++模板的一些较为少见但是非常强大的应用。为了读懂本文，你需要：
<ul>
	<li>有基础的编程知识和经验；</li>
	<li>能够看懂C++代码；</li>
	<li>对于未知的事物充满好奇心。</li>
</ul>
如果你具备上面这些条件，那么就做好准备认识你所不知道的template吧！

<!--more-->
<h2>从基础开始</h2>
为了照顾不太明白的读者，我们先很简单地讲一下什么是C++的模板。已经了然于心的读者们可以选择再来复习一遍，或者跳过这一小节。

在传统的C语言中，不同的函数不能有相同的名字。这个规定其实非常好理解，因为很多时候函数操作会依赖于特定的类型。但我们也没法排除有一些不依赖于类型的通用算法，如果要为每种类型都定义一个名字不同的函数，再在每个函数里实现一遍算法，岂不是很麻烦？

于是C++标准委员会（ISO C++ committee）就想，我们要在C++里加入一个新功能，使得程序员可以写出不依赖于具体类型的<strong>泛型</strong>（generic）代码。这个新功能就是模板。在C++中，我们可以通过如下语法定义一个模板函数：
<div class="CodeMirror cm-s-default CodeMirror-wrap">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre class=""><span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">T</span><span class="cm-operator">&gt;
</span><span class="cm-variable">T</span> <span class="cm-variable">max</span>(<span class="cm-variable">T</span> <span class="cm-variable">a</span>, <span class="cm-variable">T</span> <span class="cm-variable">b</span>) {
    <span class="cm-keyword">if</span> (<span class="cm-variable">a</span> <span class="cm-operator">&gt;</span> <span class="cm-variable">b</span>) <span class="cm-keyword">return</span> <span class="cm-variable">a</span>;
    <span class="cm-keyword">return</span> <span class="cm-variable">b</span>;
}</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
从函数名和函数的执行过程我们很容易推断出，它的作用就是找出并返回<span><code>a</code></span>和<span><code>b</code></span>中的较大值。但是这个<span><code>T</code></span>是个什么玩意儿呢？

其实<span><code>T</code></span>是在代码第一行里定义的<span><code>class T</code></span>，它代表的是一个<em>任意的类型</em>。也就是说，这个函数接受两个类型相同的东西，并返回一个同样类型的东西。

既然有了模板函数，我们也可以弄出一个模板类：
<div class="CodeMirror cm-s-default CodeMirror-wrap">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div class="CodeMirror-lines">
<div class="CodeMirror-code">
<pre class=""><span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">int</span> <span class="cm-variable">N</span>, <span class="cm-keyword">int</span> <span class="cm-variable">M</span>, <span class="cm-keyword">typename</span> <span class="cm-variable">T</span><span class="cm-operator">&gt;
</span><span class="cm-keyword">class</span> <span class="cm-variable">Matrix</span> {
<span class="cm-keyword">private</span>:
    <span class="cm-variable">T</span> <span class="cm-variable">array</span>[<span class="cm-variable">N</span>][<span class="cm-variable">M</span>];
<span class="cm-keyword">public</span>:
    <span class="cm-variable">Matrix</span>();
    <span class="cm-keyword">static</span> <span class="cm-variable">Matrix</span> <span class="cm-variable">identityMatrix</span>();
    <span class="cm-variable">T</span> <span class="cm-operator">&amp;</span><span class="cm-variable">elementAtIndex</span>(<span class="cm-keyword">int</span> <span class="cm-variable">x</span>, <span class="cm-keyword">int</span> <span class="cm-variable">y</span>) {
        <span class="cm-keyword">return</span> <span class="cm-variable">array</span>[<span class="cm-variable">x</span>][<span class="cm-variable">y</span>];
    }
    <span class="cm-keyword">const</span> <span class="cm-variable">T</span> <span class="cm-operator">&amp;</span><span class="cm-variable">elementAtIndex</span>(<span class="cm-keyword">int</span> <span class="cm-variable">x</span>, <span class="cm-keyword">int</span> <span class="cm-variable">y</span>) <span class="cm-keyword">const</span> {
        <span class="cm-keyword">return</span> <span class="cm-variable">array</span>[<span class="cm-variable">x</span>][<span class="cm-variable">y</span>];
    }
} ;</pre>
</div>
</div>
</div>
</div>
</div>
<p class="">注意到我们可以指定多个泛型的参数，而且这些参数还不一定得是<span><code>T</code></span>这样的<em>类型参数</em>——它还可以是<em>非类型参数</em><span class="">，如这里的整型</span><span class=""><code>N</code></span>，甚至可以是嵌套的模板参数。</p>
这些非常简单的求较大值的例子可以让我们略微感受到模板的强大之处：只要类型<span><code>T</code></span>可以拷贝构造、定义了大于运算符，就可以套用这个函数。如果我们有心，可以用模板实现一整套泛型的算法，并提供简单的借口。设想一下，当我们要给自定义的类型排序的时候，不需要手写快排，而定义比较操作符，直接调用一个模板函数即可；当我们要使用某些数据结构的时候，直接把我们的类名告诉模板类即可。

事实上C++的STL就是这样一个玩意儿。STL的全称是Standard Template Library，里面用模板实现了各种泛型的算法和数据结构。随叫随到，即写即用，从此无心造轮子，管他开不开O2。

限于篇幅原因，模板的基础知识只能介绍到这。有关模板的更多知识，推荐大家阅读这个网页：<a href="https://isocpp.org/wiki/faq/templates">https://isocpp.org/wiki/faq/templates</a>。
<h2>模板元编程</h2>
模板当然是个好东西，它非常之强大，可以完成原来写C代码时想都不敢想的功能。但问题也就在于它太强大了，就连当初设计模板的人也不知道它究竟有多么厉害。事实上，可以证明模板的这套语言本身就是图灵完备的，也就是说，光是使用模板，我们就可以在编译时完成一切计算。这就是所谓的<strong>模板元编程</strong>（template metaprogramming，TML）。
<h3>从斐波那契谈起</h3>
如果要你用正常的C++求斐波那契数列，你会怎么写？当然会是像下面这样：
<div class="CodeMirror cm-s-default CodeMirror-wrap">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre class=""><span class="cm-keyword">const</span> <span class="cm-keyword">int</span> <span class="cm-variable">N</span> <span class="cm-operator">=</span> <span class="cm-number">100</span>;
<span class="cm-keyword">int</span> <span class="cm-variable">a</span>[<span class="cm-variable">N</span> <span class="cm-operator">+</span> <span class="cm-number">1</span>];
<span class="cm-variable">a</span>[<span class="cm-number">0</span>] <span class="cm-operator">=</span> <span class="cm-number">0</span>, <span class="cm-variable">a</span>[<span class="cm-number">1</span>] <span class="cm-operator">=</span> <span class="cm-number">1</span>;
<span class="cm-keyword">for</span> (<span class="cm-keyword">int</span> <span class="cm-variable">i</span> <span class="cm-operator">=</span> <span class="cm-number">2</span>; <span class="cm-variable">i</span> <span class="cm-operator">&lt;=</span> <span class="cm-variable">N</span>; <span class="cm-operator">++</span><span class="cm-variable">i</span>)
    <span class="cm-variable">a</span>[<span class="cm-variable">i</span>] <span class="cm-operator">=</span> <span class="cm-variable">a</span>[<span class="cm-variable">i</span> <span class="cm-operator">-</span> <span class="cm-number">1</span>] <span class="cm-operator">+</span> <span class="cm-variable">a</span>[<span class="cm-variable">i</span> <span class="cm-operator">-</span> <span class="cm-number">2</span>];</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
或者是直接写递归的版本：
<div class="CodeMirror cm-s-default CodeMirror-wrap">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre class=""><span class="cm-keyword">int</span> <span class="cm-variable">fib</span>(<span class="cm-keyword">int</span> <span class="cm-variable">n</span>) {
    <span class="cm-keyword">if</span> (<span class="cm-variable">n</span> <span class="cm-operator">==</span> <span class="cm-number">0</span>) <span class="cm-keyword">return</span> <span class="cm-number">0</span>;
    <span class="cm-keyword">if</span> (<span class="cm-variable">n</span> <span class="cm-operator">==</span> <span class="cm-number">1</span>) <span class="cm-keyword">return</span> <span class="cm-number">1</span>;
    <span class="cm-keyword">return</span> <span class="cm-variable">fib</span>(<span class="cm-variable">n</span> <span class="cm-operator">-</span> <span class="cm-number">1</span>) <span class="cm-operator">+</span> <span class="cm-variable">fib</span>(<span class="cm-variable">n</span> <span class="cm-operator">-</span> <span class="cm-number">2</span>);
}</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
其实我们可以用模板来做：
<div class="CodeMirror cm-s-default CodeMirror-wrap ">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre class=""><span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">int</span> <span class="cm-variable">N</span><span class="cm-operator">&gt;
</span><span class="cm-keyword">struct</span> <span class="cm-variable">Fib</span> {
    <span class="cm-keyword">static</span> <span class="cm-keyword">const</span> <span class="cm-keyword">int</span> <span class="cm-variable">value</span> <span class="cm-operator">=</span> <span class="cm-variable">Fib</span><span class="cm-operator">&lt;</span><span class="cm-variable">N</span><span class="cm-operator">-</span><span class="cm-number">1</span><span class="cm-operator">&gt;</span>::<span class="cm-variable">value</span> <span class="cm-operator">+</span> <span class="cm-variable">Fib</span><span class="cm-operator">&lt;</span><span class="cm-variable">N</span><span class="cm-operator">-</span><span class="cm-number">2</span><span class="cm-operator">&gt;</span>::<span class="cm-variable">value</span>;
} ;
​
<span class="cm-keyword">template</span> <span class="cm-operator">&lt;&gt;
</span><span class="cm-keyword">struct</span> <span class="cm-variable">Fib</span><span class="cm-operator">&lt;</span><span class="cm-number">0</span><span class="cm-operator">&gt;</span> {
    <span class="cm-keyword">static</span> <span class="cm-keyword">const</span> <span class="cm-keyword">int</span> <span class="cm-variable">value</span> <span class="cm-operator">=</span> <span class="cm-number">0</span>;
} ;
​
<span class="cm-keyword">template</span> <span class="cm-operator">&lt;&gt;
</span><span class="cm-keyword">struct</span> <span class="cm-variable">Fib</span><span class="cm-operator">&lt;</span><span class="cm-number">1</span><span class="cm-operator">&gt;</span> {
    <span class="cm-keyword">static</span> <span class="cm-keyword">const</span> <span class="cm-keyword">int</span> <span class="cm-variable">value</span> <span class="cm-operator">=</span> <span class="cm-number">1</span>;
} ;</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<p class="">我们在<span><code>Fib</code></span>类里定义了一个静态的常量<span><code>value</code></span>，代表第<span><code>N</code></span>个斐波那契数的值。<span><code>N = 0</code></span>和<span><code>N = 1</code></span>的两个模板特化是递归的边界条件，一般的情况则直接利用公式递归计算。整个计算过程都是在编译时完成的，而且由于模板的实例化机制，这个递归的过程还是记忆化的，即同一个斐波那契数不会被计算两次，计算的复杂度为<span id="MathJax-Element-2-Frame" class="MathJax_SVG"></span>而非<span id="MathJax-Element-26-Frame" class="MathJax_SVG"></span>。</p>
如果我们要使用第10个斐波那契数列的值，只要用<span><code>Fib&lt;10&gt;::value</code></span>就可以了。值得一提的是，由于计算过程需要在编译时完成，模板中的参数必须得是编译时就知晓其值的常量。
<p class="">另外，通常编译器会对模板的递归层数作限制，在<span><code>clang</code></span><span class="">编译器上默认是256层。可以使用</span><span class=""><code>-ftemplate-depth=N</code></span><span class="">来将层数设为</span><span><code>N</code></span><span class="">，但太大的层数会使得编译器自己栈溢出……所以这玩意并没有什么○用。</span></p>

<h3 class=""><span class="">但是这又有什么○用呢？</span></h3>
虽说是节省了运行时间，但必须在编译时确定所有数值，加之<span><code>N</code></span>还不能太大，感觉上并没有什么○用。

但TML可不光是编译时算数这么简单。下面将介绍两个TML在实际中的应用，请坐和放宽，准备打开新世界的大门。
<h2>CRTP</h2>
这个看上去超级高大上的缩写，全称其实是Curiously Recurring Template Pattern，直译过来就是“神奇递归模板模式”，一下就显得傻里傻气的了。

为什么叫这个名字呢？我们先来看一段代码：
<div class="CodeMirror cm-s-default CodeMirror-wrap ">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre class=""><span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">Derived</span><span class="cm-operator">&gt;
</span><span class="cm-keyword">struct</span> <span class="cm-variable">Base</span> {
    <span class="cm-keyword">void</span> <span class="cm-variable">polymorphism</span>() {
        <span class="cm-keyword">static_cast</span><span class="cm-operator">&lt;</span><span class="cm-variable">Derived</span><span class="cm-operator">*&gt;</span>(<span class="cm-keyword">this</span>)<span class="cm-operator">-&gt;</span><span class="cm-variable">_poly</span>();
    }
    <span class="cm-keyword">static</span> <span class="cm-keyword">void</span> <span class="cm-variable">static_poly</span>() {
        <span class="cm-variable">Derived</span>::<span class="cm-variable">_static_poly</span>();
    }
    <span class="cm-comment">// Default implementation
</span>    <span class="cm-keyword">void</span> <span class="cm-variable">_poly</span>();
    <span class="cm-keyword">static</span> <span class="cm-variable">_static_poly</span>();
} ;
​
<span class="cm-keyword">struct</span> <span class="cm-variable">Derived</span> : <span class="cm-keyword">public</span> <span class="cm-variable">Base</span><span class="cm-operator">&lt;</span><span class="cm-variable">Derived</span><span class="cm-operator">&gt;</span> {
    <span class="cm-keyword">void</span> <span class="cm-variable">_poly</span>();
<span class="cm-comment">//  void _static_poly();
</span>}</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
嗯，<span><code>Base</code></span>类是一个正常的模板类，虽然内容有点不明觉厉。但<span><code>Derived</code></span>类是什么鬼？为啥能把自己作为父类的模板参数？这岂不是“我依赖于我爸依赖于我”的状况 = = ？

然而这段代码是可以通过编译的合法代码。原因有二：
<ul>
	<li>虽然<span><code>Base</code></span>需要了解<span><code>Derived</code></span>的定义，但其不直接或间接包含<span><code>Derived</code></span>类的实例，也即其大小不依赖于<span><code>Derived</code></span>；</li>
	<li>模板类会在被使用时实例化，而此时<span><code>Base</code></span>类与<span><code>Derived</code></span>类的定义均已知晓；就此段代码而言，编译器可以检验<span><code>Derived</code></span>类是否包含<span><code>_poly()</code></span>和<span><code>_static_poly()</code></span>函数的定义，如果没有找到，则会在其父类<span><code>Base&lt;Derived&gt;</code></span>类中寻找。</li>
</ul>
<p class=""><span class="">一般人在第一次看到这个玄学一般的用法时都会目瞪口呆不知所措。先不要惊讶，我们来看看用这种写法能做什么：</span></p>

<h3 class=""><span class="">无需VTABLE的编译时多态</span></h3>
C++实现多态的方法是将类内所有的虚（virtual）函数存在一个被称为<span><code>VTABLE</code></span>的数组中，在运行时调用一个虚函数时，实际调用表中指向的函数。这个方法有个缺点，就是必须维护这么一个表，从而产生额外开销（overhead）。
<p class="">而利用CRTP则可以在实现多态的同时，省去这个表的开销。我们看上面那段代码，在<span><code>Base</code></span>类（下称基类）和<span><code>Derived</code></span>类（下称派生类）中都定义有<span><code>_poly()</code></span>（非虚）函数，理论上基类是访问不到派生类中的函数的。但是这里不一样，基类拥有额外的信息：派生类叫什么。所以基类在调用之前，将自己转换成了派生类。这个转换是可行的，因为事实上，自己本身就是自己的派生类（有点绕，感受一下）。这么一来，调用的就是派生类中的函数了。如果派生类中没有定义<span class=""><code>_poly()</code></span><span class="">函数，则编译器会找到基类中的同名函数；如果定义了</span><span><code>_poly()</code></span><span class="">函数，则它会覆盖掉基类中的同名函数，故编译器会找到派生类中的函数。这样我们就在编译器实现了多态。</span></p>
不过CRTP的这个多态并非真正的多态。如果我们有一个派生类的实例，通过CRTP，基类中定义的函数可以调用派生类中重载的“虚”函数。但如果我们的派生类以基类指针的形式存在，我们则无法通过其访问到派生类的虚函数。后者被成为运行时多态，是CRTP力不能及的。
<h3 class=""><span class="">通用基类</span></h3>
<p class=""><span class="">有时候我们会遇到这样的问题：我们只有一个基类的指针，而我们要进行一次深拷贝，如下：</span></p>

<div class="CodeMirror cm-s-default CodeMirror-wrap ">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre><span class="cm-keyword">struct</span> <span class="cm-variable">Base</span> {};
<span class="cm-keyword">struct</span> <span class="cm-variable">Derived</span> : <span class="cm-keyword">public</span> <span class="cm-variable">Base</span> {};
<span class="cm-keyword">struct</span> <span class="cm-variable">AnotherDerived</span> : <span class="cm-keyword">public</span> <span class="cm-variable">Base</span> {};
​
<span class="cm-keyword">void</span> <span class="cm-variable">copy</span>(<span class="cm-variable">Base</span> <span class="cm-operator">*</span><span class="cm-variable">p</span>) {
    <span class="cm-variable">Base</span> <span class="cm-operator">*</span><span class="cm-variable">copy_p</span> <span class="cm-operator">=</span> <span class="cm-keyword">new</span> <span class="cm-operator">???</span>(<span class="cm-operator">*</span><span class="cm-variable">p</span>);
}</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
于是我们懵逼了，这个<span><code>???</code></span>该填啥啊，写基类不对吧，写派生类也不知道是哪个啊，咋办？
<p class="">一个解决方式定义一个名为<span><code>clone()</code></span><span class="">的虚函数，然后在每个派生类中重载：</span></p>

<div class="CodeMirror cm-s-default CodeMirror-wrap ">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre class=""><span class="cm-keyword">struct</span> <span class="cm-variable">Base</span> {
    <span class="cm-keyword">virtual</span> <span class="cm-variable">Base</span> <span class="cm-operator">*</span><span class="cm-variable">clone</span>() <span class="cm-keyword">const</span> <span class="cm-operator">=</span> <span class="cm-number">0</span>;
} ;
<span class="cm-keyword">struct</span> <span class="cm-variable">Derived</span> : <span class="cm-keyword">public</span> <span class="cm-variable">Base</span> {
    <span class="cm-keyword">virtual</span> <span class="cm-variable">Derived</span> <span class="cm-operator">*</span><span class="cm-variable">clone</span>() <span class="cm-keyword">const</span> {
        <span class="cm-keyword">return</span> <span class="cm-keyword">new</span> <span class="cm-variable">Derived</span>(<span class="cm-operator">*</span><span class="cm-keyword">this</span>);
    }
}
<span class="cm-keyword">struct</span> <span class="cm-variable">AnotherDerived</span> : <span class="cm-keyword">public</span> <span class="cm-variable">Base</span> {
    <span class="cm-keyword">virtual</span> <span class="cm-variable">AnotherDerived</span> <span class="cm-operator">*</span><span class="cm-variable">clone</span>() <span class="cm-keyword">const</span> {
        <span class="cm-keyword">return</span> <span class="cm-keyword">new</span> <span class="cm-variable">AnotherDerived</span>(<span class="cm-operator">*</span><span class="cm-keyword">this</span>);
    }
}</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
这份代码可以正常运作，但显得十分不优美：每个类里都得写一遍，这样不仅有大量重复代码，还容易出错。
<p class=""><span class="">我们来分析一下为什么需要这样写，问题似乎在于，我的基类不知道我到底是啥，所以我需要在派生类里定义虚函数。这不就是CRTP解决的问题吗？我们把代码改成下面这样：</span></p>

<div class="CodeMirror cm-s-default CodeMirror-wrap ">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre class=""><span class="cm-keyword">struct</span> <span class="cm-variable">Base</span> {
    <span class="cm-keyword">virtual</span> <span class="cm-variable">Base</span> <span class="cm-operator">*</span><span class="cm-variable">clone</span>() <span class="cm-keyword">const</span> <span class="cm-operator">=</span> <span class="cm-number">0</span>;
};
​
<span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">Derived</span><span class="cm-operator">&gt;
</span><span class="cm-keyword">struct</span> <span class="cm-variable">BaseCRTP</span> : <span class="cm-keyword">public</span> <span class="cm-variable">Base</span> {
    <span class="cm-keyword">virtual</span> <span class="cm-variable">Base</span> <span class="cm-operator">*</span><span class="cm-variable">clone</span>() <span class="cm-keyword">const</span> {
        <span class="cm-keyword">return</span> <span class="cm-keyword">new</span> <span class="cm-variable">Derived</span>(<span class="cm-keyword">static_cast</span><span class="cm-operator">&lt;</span><span class="cm-variable">Derived</span> <span class="cm-keyword">const</span> <span class="cm-operator">&amp;&gt;</span>(<span class="cm-operator">*</span><span class="cm-keyword">this</span>));
    }
}
​
<span class="cm-keyword">struct</span> <span class="cm-variable">Derived</span> : <span class="cm-keyword">public</span> <span class="cm-variable">BaseCRTP</span><span class="cm-operator">&lt;</span><span class="cm-variable">Derived</span><span class="cm-operator">&gt;</span> {};</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
假设我们有一个<span><code>Derived</code></span>类的实例，在经历了一些事情之后，它变成了<span><code>Base</code></span>类的指针。现在我们通过这个指针调用<span><code>clone()</code></span>函数，通过虚函数的机制，我们会找到<span><code>BaseCRTP&lt;Derived&gt;</code></span>类的<span><code>clone()</code></span>函数。此时我们已经知道了派生类的名字，也就可以完成深拷贝了。
<p class="">如果分析一下背后的原因，我们会发现，每次定义一个派生类，编译器都会实例化一份<span><code>BaseCRTP</code></span><span class="">出来。所以并没有什么神奇的，其实只是编译器帮我们生成了我们本应手写的代码而已。CRTP的其他应用，比如实例计数器，也都是基于这个原理。</span></p>

<h2>SFINAE</h2>
<p class=""><span class="">又是一个高大上的缩写，不急，我们先把它展开了：全称为Substitution failure is not an error。这都不是一个词组了，而是一句话：替换之错并非误（替换失败不被视为编译错误）。当替换失败时，编译器不报错，而只是将这个模板从待选的重载函数集中移除，不考虑失败的这一个模板而已。</span></p>
在实例化一个模板时，编译器需要把模板参数中的东西替换为实际的类型或值，然而替换是可能失败的，比如下面这个例子：
<div class="CodeMirror cm-s-default CodeMirror-wrap ">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre class=""><span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">T</span><span class="cm-operator">&gt;
</span><span class="cm-keyword">void</span> <span class="cm-variable">defined_foo</span>(<span class="cm-keyword">typename</span> <span class="cm-variable">T</span>::<span class="cm-variable">foo</span>) {}
​
<span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">T</span><span class="cm-operator">&gt;
</span><span class="cm-keyword">void</span> <span class="cm-variable">defined_foo</span>(<span class="cm-variable">T</span>) {}
​
<span class="cm-keyword">struct</span> <span class="cm-variable">Foo</span> {
    <span class="cm-keyword">typedef</span> <span class="cm-keyword">int</span> <span class="cm-variable">foo</span>;
} ;</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<p class="">这里我们定义了两个版本的<span><code>defined_foo()</code></span>，接受不同的参数。<span><code>typename</code></span>关键字是为了消除歧义，告诉编译器<span><code>T::foo</code></span><span class="">绝壁是个类型名。由于是模板函数，编译器得做类型替换，这里就可能出现替换失败的情况，比如：</span></p>

<div class="CodeMirror cm-s-default CodeMirror-wrap ">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre class=""><span class="cm-variable">defined_foo</span><span class="cm-operator">&lt;</span><span class="cm-variable">Foo</span><span class="cm-operator">&gt;</span>(<span class="cm-number">0</span>);
<span class="cm-variable">defined_foo</span><span class="cm-operator">&lt;</span><span class="cm-keyword">int</span><span class="cm-operator">&gt;</span>(<span class="cm-number">0</span>);</pre>
</div>
</div>
</div>
</div>
</div>
<div></div>
</div>
</div>
第一行，<span><code>Foo</code></span>类中有<span><code>foo</code></span>类型，所以在第一个模板中替换成功，而在第二个模板中替换失败；第二行，<span><code>int</code></span>类型显然不包含别的类型，所以在第一个模板中替换失败，而在第二个模板中替换成功。这两个情况中，都是恰有一个重载的模板替换成功，都不会报错。
<p class=""><span class="">看上去是非常自然而且简单的做法吧？你绝对想不到可以用这玩意儿做什么：</span></p>

<h3>编译时自省</h3>
先说说什么是自省。子曰：“见贤思齐焉，见不贤而内<em>自省</em>也”，不过这和我们要讲的自省没有半毛钱关系。

所谓自省，其实就是程序自己知道自己的情况。比如在Python中，程序可以在运行时查看自己的代码，获取某个类的名称、成员，甚至是实时增减成员。对于C++这种强类型语言来说，这显然不现实。但通过SFINAE，我们可以实现一定程度上的自省。
<p class="">上面的那个例子，其实就是某种自省。我们可以在编译时判断一个类是否满足某种条件，从而调用不同的函数。在STL中，有一个神秘的头文件叫<span><code>&lt;type_traits&gt;</code></span>（C++11的新特性），其中包含了很多判断类型的类型的东西，如<span><code>is_array</code></span>、<span><code>is_class</code></span>等。这些东西其实是用SFINAE实现的模板类，其中包含一个名为<span class=""><code>value</code></span><span class="">的成员，代表判断的值是真是假。</span></p>
这么说可能有些玄，我们来看一个具体的、真实存在的例子：
<h3>boost::enable_if</h3>
<p class=""><span class="">先上代码再解释：</span></p>

<div class="CodeMirror cm-s-default CodeMirror-wrap ">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre class=""><span class="cm-keyword">template</span><span class="cm-operator">&lt;</span><span class="cm-keyword">bool</span> <span class="cm-variable">Cond</span>, <span class="cm-keyword">class</span> <span class="cm-variable">T</span> <span class="cm-operator">=</span> <span class="cm-keyword">void</span><span class="cm-operator">&gt;
</span><span class="cm-keyword">struct</span> <span class="cm-variable">enable_if_c
</span>{ <span class="cm-keyword">typedef</span> <span class="cm-variable">T</span> <span class="cm-variable">type</span>; };
​
<span class="cm-keyword">template</span><span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">T</span><span class="cm-operator">&gt;
</span><span class="cm-keyword">struct</span> <span class="cm-variable">enable_if_c</span><span class="cm-operator">&lt;</span><span class="cm-atom">false</span>, <span class="cm-variable">T</span><span class="cm-operator">&gt;</span> {};
​
<span class="cm-keyword">template</span><span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">Cond</span>, <span class="cm-keyword">class</span> <span class="cm-variable">T</span> <span class="cm-operator">=</span> <span class="cm-keyword">void</span><span class="cm-operator">&gt;
</span><span class="cm-keyword">struct</span> <span class="cm-variable">enable_if</span> : <span class="cm-variable">enable_if_c</span><span class="cm-operator">&lt;</span><span class="cm-variable">Cond</span>::<span class="cm-variable">value</span>, <span class="cm-variable">T</span><span class="cm-operator">&gt;</span> {};
​
<span class="cm-comment">// === 我是分割线 ===
</span><span class="cm-keyword">template</span><span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">T</span><span class="cm-operator">&gt;
</span><span class="cm-keyword">typename</span> <span class="cm-variable">enable_if</span><span class="cm-operator">&lt;</span><span class="cm-variable">is_floating_point</span><span class="cm-operator">&lt;</span><span class="cm-variable">T</span><span class="cm-operator">&gt;</span>, <span class="cm-variable">T</span><span class="cm-operator">&gt;</span>::<span class="cm-variable">type
</span>  <span class="cm-variable">frobnicate</span>(<span class="cm-variable">T</span> <span class="cm-variable">t</span>) {}</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<span><code>is_floating_point</code></span>也是<span><code>&lt;type_traits&gt;</code></span>里的东西，其作用正如其名。<span><code>enable_if</code></span>的实现是，如果第一个参数的条件为<span><code>true</code></span>，那么其中会包含一个<span><code>type</code></span>类型，为第二个参数的类型；否则不会有这个类型。拿分割线下的函数举例，如果第一个参数的条件，即<span><code>T</code></span>是浮点类型，为<span><code>false</code></span>，那么在使用这个函数时就会产生替换错误。所以<span><code>enable_if</code></span>的作用可以理解为限制模板所能够接受的类型。

不过这个写法有点丑陋，毕竟返回类型这么长；所以另一个常见的写法是添加一个虚设的（dummy）参数，并在参数里实现SFINAE：
<div class="CodeMirror cm-s-default CodeMirror-wrap ">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre class=""><span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">T</span><span class="cm-operator">&gt;
</span><span class="cm-variable">T</span> <span class="cm-variable">frobnicate</span>(<span class="cm-variable">T</span> <span class="cm-variable">t</span>, <span class="cm-keyword">typename</span> <span class="cm-variable">enable_if</span><span class="cm-operator">&lt;</span><span class="cm-variable">is_floating_point</span><span class="cm-operator">&lt;</span><span class="cm-variable">T</span><span class="cm-operator">&gt;</span>, <span class="cm-variable">T</span><span class="cm-operator">&gt;</span>::<span class="cm-variable">type</span> <span class="cm-operator">*</span> <span class="cm-operator">=</span> <span class="cm-number">0</span>) {}</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<h3>type_traits</h3>
<p class="">整个<span><code>&lt;type_traits&gt;</code></span>库过于庞大，以个人之力无法看穿，只能挑选一些浅薄的所见所得与大家分享。下面的代码均来自<span><code>&lt;type_traits&gt;</code></span><span class="">库，为了便于阅读，删去了一部分不影响理解的内容。</span></p>
首先，我们需要两个类<span><code>true_type</code></span>和<span><code>false_type</code></span>，用来区分结果。这两个类中应当定义<span><code>value</code></span>，类型为<span><code>bool</code></span>，值分别为<span><code>true</code></span>和<span><code>false</code></span>。

然后，我们要考虑cv修饰符（<span><code>const</code></span>和<span><code>volatile</code></span>）的问题，它们不应影响我们对类型的判断。<span><code>remove_cv</code></span>的作用是去除类型中的cv修饰符，它的实现如下：
<div class="CodeMirror cm-s-default CodeMirror-wrap ">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre class=""><span class="cm-comment">// remove_const
</span><span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">_Tp</span><span class="cm-operator">&gt;</span> <span class="cm-keyword">struct</span> <span class="cm-variable">remove_const</span> {<span class="cm-keyword">typedef</span> <span class="cm-variable">_Tp</span> <span class="cm-variable">type</span>;};
<span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">_Tp</span><span class="cm-operator">&gt;</span> <span class="cm-keyword">struct</span> <span class="cm-variable">remove_const</span><span class="cm-operator">&lt;</span><span class="cm-keyword">const</span> <span class="cm-variable">_Tp</span><span class="cm-operator">&gt;</span> {<span class="cm-keyword">typedef</span> <span class="cm-variable">_Tp </span><span class="cm-variable">type</span>;};
​
<span class="cm-comment">// remove_volatile
</span><span class="cm-comment">// ...
</span>​
<span class="cm-comment">// remove_cv
</span><span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">_Tp</span><span class="cm-operator">&gt;</span> <span class="cm-keyword">struct</span> <span class="cm-variable">remove_cv</span> {
    <span class="cm-keyword">typedef</span> <span class="cm-keyword">typename</span> <span class="cm-variable">remove_volatile</span><span class="cm-operator">&lt;</span><span class="cm-keyword">typename </span><span class="cm-variable">remove_const</span><span class="cm-operator">&lt;</span><span class="cm-variable">_Tp</span><span class="cm-operator">&gt;</span>::<span class="cm-variable">type</span><span class="cm-operator">&gt;</span>::<span class="cm-variable">type</span> <span class="cm-variable">type</span>;
};</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<p class="">这里利用模板的匹配功能，获得了类型去掉cv修饰符后的名字，并记在了<span class=""><code>remove_cv::type</code></span><span class="">中。</span></p>
<p class=""><span class="">接下来就可以判断了。先看一个简单的，</span><span class=""><code>is_null_pointer</code></span><span class="">：</span></p>

<div class="CodeMirror cm-s-default CodeMirror-wrap ">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre><span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">_Tp</span><span class="cm-operator">&gt;</span> <span class="cm-keyword">struct</span> <span class="cm-variable">__is_nullptr_t_impl</span> : <span class="cm-keyword">public</span> <span class="cm-variable">false_type</span> {};
<span class="cm-keyword">template</span> <span class="cm-operator">&lt;&gt;</span> <span class="cm-keyword">struct</span> <span class="cm-variable">__is_nullptr_t_impl</span><span class="cm-operator">&lt;</span><span class="cm-variable">nullptr_t</span><span class="cm-operator">&gt;</span> : <span class="cm-keyword">public</span> <span class="cm-variable">true_type</span> {};
​
<span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">_Tp</span><span class="cm-operator">&gt;</span> <span class="cm-keyword">struct</span> <span class="cm-variable">_LIBCPP_TYPE_VIS_ONLY</span> <span class="cm-variable">is_null_pointer
</span>    : <span class="cm-keyword">public</span> <span class="cm-variable">__is_nullptr_t_impl</span><span class="cm-operator">&lt;</span><span class="cm-keyword">typename</span> <span class="cm-variable">remove_cv</span><span class="cm-operator">&lt;</span><span class="cm-variable">_Tp</span><span class="cm-operator">&gt;</span>::<span class="cm-variable">type</span><span class="cm-operator">&gt;</span> {};</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<span><code>nullptr_t</code></span>是C++11中加入的空指针类型。这个判断很简单，不用多说。
<p class="">下面则是一个特别玄的判断：<span><code>is_base_of</code></span>，判断<span><code>B</code></span>是否是<span><code>D</code></span>的基类。由于<span class=""><code>&lt;type_traits&gt;</code></span><span class="">中的代码过于复杂，下面给出的代码是StackOverflow上一个问题里的简化版代码：</span></p>

<div class="CodeMirror cm-s-default CodeMirror-wrap ">
<div class="CodeMirror-scroll">
<div class="CodeMirror-sizer">
<div>
<div class="CodeMirror-lines">
<div>
<div class="CodeMirror-code">
<pre><span class="cm-keyword">typedef</span> <span class="cm-keyword">char</span> (<span class="cm-operator">&amp;</span><span class="cm-variable">yes</span>)[<span class="cm-number">1</span>];
<span class="cm-keyword">typedef</span> <span class="cm-keyword">char</span> (<span class="cm-operator">&amp;</span><span class="cm-variable">no</span>)[<span class="cm-number">2</span>];
​
<span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">B</span>, <span class="cm-keyword">class</span> <span class="cm-variable">D</span><span class="cm-operator">&gt;
</span><span class="cm-keyword">struct</span> <span class="cm-variable">Host</span> {
    <span class="cm-keyword">operator</span> <span class="cm-variable">B</span><span class="cm-operator">*</span>() <span class="cm-keyword">const</span>;
    <span class="cm-keyword">operator</span> <span class="cm-variable">D</span><span class="cm-operator">*</span>();
} ;
​
<span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">B</span>, <span class="cm-keyword">class</span> <span class="cm-variable">D</span><span class="cm-operator">&gt;
</span><span class="cm-keyword">struct</span> <span class="cm-variable">is_base_of</span> {
    <span class="cm-keyword">template</span> <span class="cm-operator">&lt;</span><span class="cm-keyword">class</span> <span class="cm-variable">T</span><span class="cm-operator">&gt;
</span>      <span class="cm-keyword">static</span> <span class="cm-variable">yes</span> <span class="cm-variable">test</span>(<span class="cm-variable">D</span><span class="cm-operator">*</span>, <span class="cm-variable">T</span>);
    <span class="cm-keyword">static</span> <span class="cm-variable">no</span> <span class="cm-variable">test</span>(<span class="cm-variable">B</span><span class="cm-operator">*</span>, <span class="cm-keyword">int</span>);
 
    <span class="cm-keyword">static</span> <span class="cm-keyword">const</span> <span class="cm-keyword">bool</span> <span class="cm-variable">value</span> <span class="cm-operator">=</span> <span class="cm-keyword">sizeof</span>(<span class="cm-variable">test</span>(<span class="cm-variable">Host</span><span class="cm-operator">&lt;</span><span class="cm-variable">B</span>,<span class="cm-variable">D</span><span class="cm-operator">&gt;</span>(), <span class="cm-keyword">int</span>())) <span class="cm-operator">==</span> <span class="cm-keyword">sizeof</span>(<span class="cm-variable">yes</span>);
} ;</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
我们把<span><code>true_type</code></span>和<span><code>false_type</code></span>改成了<span><code>yes</code></span>和<span><code>no</code></span>，通过其内存大小来判断类型；这个并不重要。
<p class=""><span class="">要理解这段代码的原理，首先我们需要知道C++标准中，有多个可选函数时会优先选择哪方：</span></p>

<ul>
	<li>
<p class=""><span class=""><strong>原则1：</strong></span><span class="">如果两个函数参数类型相同，而cv修饰符不同，则优先选择与传入参数cv修饰符匹配的一方；</span></p>
</li>
	<li>
<p class=""><span class=""><strong>原则2：</strong></span><span class="">如果原则1无法区分，且两个类型转换函数返回类型不同，则优先选择与目标参数匹配的一方；</span></p>
</li>
	<li>
<p class=""><span class=""><strong>原则3：</strong></span><span class="">如果原则2无法区分，优先选择非模板函数。</span></p>
</li>
</ul>
现在我们来分析一下代码的原理。<span><code>Host</code></span>的两个类型转换函数的原型分别为
<ul>
	<li>
<p class=""><span class=""><code>B *(Host&lt;B, D&gt; const &amp;)</code></span></p>
</li>
	<li>
<p class=""><span class=""><code>D *(Host&lt;B, D&gt; &amp;)</code></span></p>
</li>
</ul>
假设<span><code>B</code></span>是<span><code>D</code></span>的基类，那么<span><code>D *</code></span>可以转换为<span><code>B *</code></span>，反则不行。对于第一个<span><code>test</code></span>函数，可选的转换函数只有第二个；而对于第二个<span><code>test</code></span>函数，两个转换函数都可选，根据原则1，编译器会第二个选择转换函数（因为默认的传入参数为<span><code>*this</code></span>，为非<span><code>const</code></span>类型）。此时第一个<span><code>test</code></span>函数的目标类型与转换函数的返回类型匹配，而第二个的不匹配，根据原则2，编译器会选择第一个<span><code>test</code></span>函数，故得到<span><code>yes</code></span>。
<p class="">假设<span><code>B</code></span>不是<span><code>D</code></span>的基类，那么<span><code>D *</code></span>不可以转换为<span><code>B *</code></span>，反之或许可以。对于第二个<span><code>test</code></span>函数，可选的转换函数只有第一个；对于第一个<span><code>test</code></span><span class="">函数，可选的转换函数有第二个，也可能有第一个，但一定会选择第二个。此时根据原则3，编译器会选择第二个</span><span><code>test</code></span>函数，故得到<span><code>no</code></span>。</p>

<h2 class=""><span class="">总结</span></h2>
写了这么多，其实也只涉及了模板的冰山一角。由此可见这一功能的强大与复杂，也不难理解为何模板一直处于争论的中心，甚至有这么一个笑话：“Java程序员聚在一起谈面向对象和设计模式，C++程序员聚在一起谈模板和语言规范到底是怎么回事”，嘲笑的就是C++令人咂舌的复杂程度。
<p class=""><span class="">但一码归一码，C++还是一门被广泛使用的语言，因此适当的了解还是必要的。而本文中提到的用法，可能大家没有见过，但在工程中的确是普遍存在的。就拿CRTP来说，Boost库的文法分析库Spirit、计算几何库CGAL的整个核心中都使用了CRTP；而SFINAE更是已经进入了C++标准。即便自己不会写出这样的代码，至少在见到的时候也应该明白是在干什么；退一步讲，把这篇文章当成是普通的科普文章，图个乐呵也好。</span></p>

<h2 class=""><span class="">参考文献</span></h2>
<ul class="">
	<li>
<p class=""><span class=""><a href="https://en.wikipedia.org/wiki/Curiously_recurring_template_pattern">Wikipedia - Curiously recurring template pattern</a></span></p>
</li>
	<li>
<p class=""><span class=""><a href="https://katyscode.wordpress.com/2013/08/22/c-polymorphic-cloning-and-the-crtp-curiously-recurring-template-pattern/">Katy’s Code - C++: Polymorphic cloning and the CRTP (Curiously Recurring Template Pattern)</a></span></p>
</li>
	<li>
<p class=""><span class=""><a href="https://en.wikipedia.org/wiki/Substitution_failure_is_not_an_error">Wikipedia - Substitution failure is not an error</a></span></p>
</li>
	<li><a href="http://accu.org/content/conf2013/Jonathan_Wakely_sfinae.pdf">ACCU 2013 - Jonathan Wakely - SFINAE Functionality Is Not Arcane Esoterica</a></li>
	<li>
<p class=""><span class=""><a href="http://stackoverflow.com/questions/2910979/how-does-is-base-of-work">StackOverflow - How does <span class=""><code>is_base_of</code></span> work?</a></span></p>
</li>
</ul>
&nbsp;
