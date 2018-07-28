## foreach的引用赋值

我们先来做一道试题，进而思考一下foreach的引用赋值。

题目：请写出以下php语句执行输出的结果：  

    <span class="php"><span class="hljs-preprocessor"><?php</span>
        <span class="hljs-variable">$arr</span> = [<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>];  
        <span class="hljs-keyword">foreach</span>(<span class="hljs-variable">$arr</span> <span class="hljs-keyword">as</span> &<span class="hljs-variable">$value</span>){
            <span class="hljs-comment"># code...</span>
        }
        <span class="hljs-keyword">foreach</span>(<span class="hljs-variable">$arr</span> <span class="hljs-keyword">as</span> <span class="hljs-variable">$value</span>){
            <span class="hljs-comment"># code...</span>
        }
        print_r(<span class="hljs-variable">$arr</span>);</span>
    `</pre>

    答案：

    <pre>`    <span class="hljs-keyword">Array</span> ( [<span class="hljs-number">0</span>] => <span class="hljs-number">1</span> [<span class="hljs-number">1</span>] => <span class="hljs-number">2</span> [<span class="hljs-number">2</span>] => <span class="hljs-number">2</span> ) 
    `</pre>

    怎么样？这样的输出结果是如你所愿呢？还是出乎了意料呢？

    * * *

    那下面我们来分析一下为何会有这样的结果。如下：

    <pre>`<span class="php"><span class="hljs-preprocessor"><?php</span>
        <span class="hljs-variable">$arr</span> = [<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>];  
        <span class="hljs-keyword">foreach</span>(<span class="hljs-variable">$arr</span> <span class="hljs-keyword">as</span> &<span class="hljs-variable">$value</span>){
            var_dump(<span class="hljs-variable">$arr</span>);
            <span class="hljs-keyword">echo</span> <span class="hljs-string">"<br/>"</span>;
        }
        <span class="hljs-keyword">echo</span> <span class="hljs-string">"<br />"</span>;
        <span class="hljs-keyword">foreach</span>(<span class="hljs-variable">$arr</span> <span class="hljs-keyword">as</span> <span class="hljs-variable">$value</span>){
            var_dump(<span class="hljs-variable">$arr</span>);
            <span class="hljs-keyword">echo</span> <span class="hljs-string">"<br/>"</span>;
        }</span>
    `</pre><pre>`    输出：array(<span class="hljs-number">3</span>) { [<span class="hljs-number">0</span>]=> &<span class="hljs-built_in">int</span>(<span class="hljs-number">1</span>) [<span class="hljs-number">1</span>]=> <span class="hljs-built_in">int</span>(<span class="hljs-number">2</span>) [<span class="hljs-number">2</span>]=> <span class="hljs-built_in">int</span>(<span class="hljs-number">3</span>) } 
             array(<span class="hljs-number">3</span>) { [<span class="hljs-number">0</span>]=> <span class="hljs-built_in">int</span>(<span class="hljs-number">1</span>) [<span class="hljs-number">1</span>]=> &<span class="hljs-built_in">int</span>(<span class="hljs-number">2</span>) [<span class="hljs-number">2</span>]=> <span class="hljs-built_in">int</span>(<span class="hljs-number">3</span>) } 
             array(<span class="hljs-number">3</span>) { [<span class="hljs-number">0</span>]=> <span class="hljs-built_in">int</span>(<span class="hljs-number">1</span>) [<span class="hljs-number">1</span>]=> <span class="hljs-built_in">int</span>(<span class="hljs-number">2</span>) [<span class="hljs-number">2</span>]=> &<span class="hljs-built_in">int</span>(<span class="hljs-number">3</span>) } 

             array(<span class="hljs-number">3</span>) { [<span class="hljs-number">0</span>]=> <span class="hljs-built_in">int</span>(<span class="hljs-number">1</span>) [<span class="hljs-number">1</span>]=> <span class="hljs-built_in">int</span>(<span class="hljs-number">2</span>) [<span class="hljs-number">2</span>]=> &<span class="hljs-built_in">int</span>(<span class="hljs-number">1</span>) } 
             array(<span class="hljs-number">3</span>) { [<span class="hljs-number">0</span>]=> <span class="hljs-built_in">int</span>(<span class="hljs-number">1</span>) [<span class="hljs-number">1</span>]=> <span class="hljs-built_in">int</span>(<span class="hljs-number">2</span>) [<span class="hljs-number">2</span>]=> &<span class="hljs-built_in">int</span>(<span class="hljs-number">2</span>) } 
             array(<span class="hljs-number">3</span>) { [<span class="hljs-number">0</span>]=> <span class="hljs-built_in">int</span>(<span class="hljs-number">1</span>) [<span class="hljs-number">1</span>]=> <span class="hljs-built_in">int</span>(<span class="hljs-number">2</span>) [<span class="hljs-number">2</span>]=> &<span class="hljs-built_in">int</span>(<span class="hljs-number">2</span>) }
    `</pre>

    当我们在两个foreach语句的循环体中增加了`var_dump()`时就很容易看出其中引用赋值对foreach语句的执行带来的变化。  

    原因分析：foreach循环遍历时，会将数组的键和值分别赋值给变量`$key`（上述未定义）和`$value`，相当于`$value = $arr[$key]`。如果是`&$value`时，则`$value = &$arr[$key]`。故第一个foreach循环时，通过`var_dump()`打印会看到三次分别再`$arr[0],$arr[1],$arr[2]`处存在引用赋值。而第二个foreach循环时，因`$arr[2]`的地址指向和`$value`是同一内存空间，所以会看到第二次foreach循环时`$arr[2]`的值会分别是`1,2,3`。

    （注：foreach循环执行完毕后并不会释放`$key`和`$value`，所以在你的代码中foreach执行完毕后请谨慎时候`$key`和`$value`，尤其是引用赋值。）

    * * *

    不知道上述是否能让你有所收获，结尾我们还有一道题，来看一下吧。

    题目：请写出以下php语句执行输出结果：  

    <pre>`<span class="php"><span class="hljs-preprocessor"><?php</span>
        <span class="hljs-variable">$arr</span> = [<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>];  
        <span class="hljs-keyword">foreach</span>(<span class="hljs-variable">$arr</span> <span class="hljs-keyword">as</span> <span class="hljs-variable">$value</span>){
            <span class="hljs-keyword">unset</span>(<span class="hljs-variable">$value</span>);
        }
        print_r(<span class="hljs-variable">$arr</span>);

        <span class="hljs-keyword">foreach</span>(<span class="hljs-variable">$arr</span> <span class="hljs-keyword">as</span> &<span class="hljs-variable">$value</span>){
            <span class="hljs-keyword">unset</span>(<span class="hljs-variable">$value</span>);
        }
        print_r(<span class="hljs-variable">$arr</span>);</span>
    