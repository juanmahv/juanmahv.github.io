---


---

<p>One of the classical as well as mainly ignored principles of software engineering is the Principle of Least Knowledge aka <em>Law of Demeter</em>. There is many academical essays in the web about this, but the main point it’s not so easy to grasp in my opinion, so I’ll give a shot in explaining this concept from a developer perspective.</p>
<p>Imagine  a class <em>Customer</em> that offers a pointer to  class <em>Wallet</em>, so for each payment, the Customer just delegates into <em>Wallet</em>.</p>
<pre class=" language-c"><code class="prism ++ language-c">class Customer 
<span class="token punctuation">{</span>
public<span class="token punctuation">:</span>
<span class="token function">Customer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        myWallet <span class="token operator">=</span> new <span class="token function">Wallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        myWallet<span class="token operator">-&gt;</span><span class="token function">addMoney</span><span class="token punctuation">(</span><span class="token number">200</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
 <span class="token punctuation">}</span>

<span class="token operator">~</span><span class="token function">Customer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>  delete myWallet<span class="token punctuation">;</span> <span class="token punctuation">}</span>

Wallet<span class="token operator">*</span> <span class="token function">getWallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token keyword">return</span> myWallet<span class="token punctuation">;</span> <span class="token punctuation">}</span>

private<span class="token punctuation">:</span>
    Wallet<span class="token operator">*</span> myWallet<span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>and the class Wallet takes care of the cash by itself:</p>
<pre class=" language-c"><code class="prism ++ language-c">class Wallet 
<span class="token punctuation">{</span>
public<span class="token punctuation">:</span>
    <span class="token function">Wallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
    <span class="token punctuation">:</span><span class="token function">currentMoney</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span><span class="token punctuation">}</span>

    <span class="token keyword">void</span> <span class="token function">addMoney</span><span class="token punctuation">(</span><span class="token keyword">int</span> amount<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        currentMoney <span class="token operator">+</span><span class="token operator">=</span> amount<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">const</span> <span class="token keyword">int</span> <span class="token function">howMuchMoney</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> currentMoney<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">int</span> <span class="token function">retrieveMoney</span><span class="token punctuation">(</span><span class="token keyword">int</span> amount<span class="token punctuation">)</span> <span class="token punctuation">{</span>
       <span class="token keyword">int</span> moneyTaken<span class="token punctuation">;</span>
       <span class="token keyword">if</span> <span class="token punctuation">(</span>amount <span class="token operator">&lt;=</span> currentMoney<span class="token punctuation">)</span> <span class="token punctuation">{</span>
           moneyTaken <span class="token operator">=</span> amount<span class="token punctuation">;</span>
           currentMoney <span class="token operator">-</span><span class="token operator">=</span> amount<span class="token punctuation">;</span>
       <span class="token punctuation">}</span>
       <span class="token keyword">else</span> <span class="token punctuation">{</span>
           moneyTaken <span class="token operator">=</span> currentMoney<span class="token punctuation">;</span>
           currentMoney <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>
       <span class="token punctuation">}</span>
       <span class="token keyword">return</span> moneyTaken<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

private<span class="token punctuation">:</span>
    <span class="token keyword">int</span> currentMoney<span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>For each payment, <em>Customer</em> just delegates to <em>Wallet</em>, as you see in the following lines:</p>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>

<span class="token punctuation">{</span>

Customer customer<span class="token punctuation">;</span>
<span class="token keyword">int</span>  customerMoney <span class="token operator">=</span> customer<span class="token punctuation">.</span><span class="token function">getWallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token operator">-&gt;</span><span class="token function">howMuchMoney</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

std<span class="token punctuation">:</span><span class="token punctuation">:</span>cout <span class="token operator">&lt;&lt;</span> <span class="token string">"Money, "</span> <span class="token operator">&lt;&lt;</span> customerMoney <span class="token operator">&lt;&lt;</span> <span class="token string">"!\n"</span><span class="token punctuation">;</span> <span class="token comment">// Money, 200!</span>

<span class="token comment">//Buy</span>
bool waspaid <span class="token operator">=</span> customer<span class="token punctuation">.</span><span class="token function">getWallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token operator">-&gt;</span><span class="token function">retrieveMoney</span><span class="token punctuation">(</span><span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

std<span class="token punctuation">:</span><span class="token punctuation">:</span>cout <span class="token operator">&lt;&lt;</span> <span class="token string">"paid, "</span> <span class="token operator">&lt;&lt;</span> <span class="token punctuation">(</span>waspaid<span class="token operator">?</span><span class="token string">"yes"</span><span class="token punctuation">:</span><span class="token string">"no"</span><span class="token punctuation">)</span> <span class="token operator">&lt;&lt;</span> <span class="token string">"!\n"</span><span class="token punctuation">;</span> <span class="token comment">//paid, yes!</span>
std<span class="token punctuation">:</span><span class="token punctuation">:</span>cout <span class="token operator">&lt;&lt;</span> <span class="token string">"current Money, "</span> <span class="token operator">&lt;&lt;</span> customer<span class="token punctuation">.</span><span class="token function">getWallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token operator">-&gt;</span><span class="token function">howMuchMoney</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">&lt;&lt;</span> <span class="token string">"!\n"</span><span class="token punctuation">;</span> 
<span class="token comment">//current Money, 197!</span>

<span class="token punctuation">}</span>
</code></pre>
<p>So far so good. By just calling <em>customer.getWallet()</em> class Customer shifts the work to <em>Wallet</em>. As <em>Wallet</em> know how to deal with the cash flow, all is well.</p>
<p>Well?, well, let’s say in an update, <em>Customer</em> has to deal with 2 Wallets (I’m old enough to remember the times before €, when I had to carry 2 wallets around, one with Spanish Pesetas and other with German D-Marks. Fortunately not anymore).  Translating that to our code, it means the <em>Customer</em> need a second instance of <em>Wallet</em>, a situation we can code like this:</p>
<pre class=" language-c"><code class="prism ++ language-c">class Customer <span class="token punctuation">{</span>

public<span class="token punctuation">:</span>
    <span class="token function">Customer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        myWallets<span class="token punctuation">.</span><span class="token function">push_back</span><span class="token punctuation">(</span>new <span class="token function">Wallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        myWallets<span class="token punctuation">.</span><span class="token function">push_back</span><span class="token punctuation">(</span>new <span class="token function">Wallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        myWallets<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token operator">-&gt;</span><span class="token function">addMoney</span><span class="token punctuation">(</span><span class="token number">200</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        myWallets<span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">]</span><span class="token operator">-&gt;</span><span class="token function">addMoney</span><span class="token punctuation">(</span><span class="token number">50</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token operator">~</span><span class="token function">Customer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
  <span class="token keyword">for</span> <span class="token punctuation">(</span>std<span class="token punctuation">:</span><span class="token punctuation">:</span>vector<span class="token operator">&lt;</span>Wallet<span class="token operator">*</span><span class="token operator">&gt;</span><span class="token punctuation">:</span><span class="token punctuation">:</span>iterator it <span class="token operator">=</span> myWallets<span class="token punctuation">.</span><span class="token function">begin</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">;</span> it <span class="token operator">!=</span> myWallets<span class="token punctuation">.</span><span class="token function">end</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token operator">++</span>it<span class="token punctuation">)</span>
     <span class="token function">delete</span><span class="token punctuation">(</span><span class="token operator">*</span>it<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

Wallet<span class="token operator">*</span> <span class="token function">getWallet</span><span class="token punctuation">(</span><span class="token keyword">unsigned</span> <span class="token keyword">int</span> pos<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>pos <span class="token operator">&lt;</span> <span class="token number">2</span><span class="token punctuation">)</span>
            <span class="token keyword">return</span> myWallets<span class="token punctuation">[</span>pos<span class="token punctuation">]</span><span class="token punctuation">;</span>
        <span class="token keyword">else</span>
            <span class="token keyword">return</span> <span class="token constant">NULL</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

private<span class="token punctuation">:</span>
       std<span class="token punctuation">:</span><span class="token punctuation">:</span>vector<span class="token operator">&lt;</span>Wallet<span class="token operator">*</span><span class="token operator">&gt;</span> myWallets<span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>But now, the code inside Main() is broken. We need to rewrite it and tell <em>which Wallet</em> to get the money from.</p>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{</span>
  Customer customer<span class="token punctuation">;</span>
  <span class="token keyword">int</span> customerMoney <span class="token operator">=</span> customer<span class="token punctuation">.</span><span class="token function">getWallet</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token operator">-&gt;</span><span class="token function">getCurrentMoney</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  std<span class="token punctuation">:</span><span class="token punctuation">:</span>cout <span class="token operator">&lt;&lt;</span> <span class="token string">"Money, "</span> <span class="token operator">&lt;&lt;</span> customerMoney <span class="token operator">&lt;&lt;</span> <span class="token string">"!\n"</span><span class="token punctuation">;</span> <span class="token comment">//Money, 200!</span>
<span class="token punctuation">}</span>
</code></pre>
<p>You can imagine that <strong>all</strong> places where <em>customer.GetWallet()</em> is invoked now need correction. And depending of your code, this can be a lot of work. It’s even worse if <em>Customer</em> is shipped as part of a library and you have no access to the code which makes use of the <em>Wallet</em>.</p>
<p>This is not only a design flow, but sometimes also a security issue. You cannot assume that clients use your API in the way you expect:</p>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">{</span>
  <span class="token comment">//Opps!</span>
  Customer<span class="token operator">*</span> victim <span class="token operator">=</span> <span class="token operator">&amp;</span>customer<span class="token punctuation">;</span>
  victim<span class="token operator">-&gt;</span><span class="token function">getWallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token operator">-&gt;</span><span class="token function">retrieveMoney</span><span class="token punctuation">(</span>victim<span class="token operator">-&gt;</span><span class="token function">getWallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token operator">-&gt;</span><span class="token function">howMuchMoney</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  std<span class="token punctuation">:</span><span class="token punctuation">:</span>cout <span class="token operator">&lt;&lt;</span> <span class="token string">"Money left?, "</span> <span class="token operator">&lt;&lt;</span> victim<span class="token operator">-&gt;</span><span class="token function">getWallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token operator">-&gt;</span><span class="token function">howMuchMoney</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">&lt;&lt;</span> <span class="token string">"!\n"</span><span class="token punctuation">;</span> 
  <span class="token comment">//Money left?, 0!</span>
<span class="token punctuation">}</span>
</code></pre>
<p>But wait a second, why should <em>Main()</em> have to choose between wallets? Why should it know that there are two?<br>
This imply a level of knowledge that no one from outside <em>Customer</em> class should know.  What’s the problem here?</p>
<ul>
<li>We are exposing customer’s internal structure.</li>
<li>There is a high coupling between Wallet and customer’s clients.</li>
<li>The relationship <em>has-a</em> between <em>Customer</em> and <em>Wallet</em> is <em>de facto</em> part of the Customer interface to the outside world.</li>
</ul>
<p>To sum up: We are breaking the Law of Demeter.</p>
<h3 id="law-of-demeter">Law of Demeter</h3>
<p>According to <a href="https://en.wikipedia.org/wiki/Law_of_Demeter">Wikipedia</a>, The Law of Demeter for functions requires that a method <strong>m</strong> of an object <strong>O</strong> may only invoke the methods of the following kinds of objects:</p>
<pre><code>O itself
m's parameters
Any objects created/instantiated within m
O's direct component objects
A global variable, accessible by O, in the scope of m
</code></pre>
<p>In particular, an object should avoid invoking methods of a member object returned by another method.<br>
Demeter law set restrictions on the <strong>Types</strong> of objects we can talk with.<br>
In side a method, we can only talk to <em>friends</em>:</p>
<pre><code>Myself
Method parameters
Objects created by the method
Direct components of my class
Global variables
</code></pre>
<p>All other Types are <em>strangers</em>. It’s not allowed invoking method of an object returned by another method: The friends of my friends are strangers to me.</p>
<p>In the code snipped above, class <em>Wallet</em> is clearly an strange for <em>Main()</em> method.</p>
<blockquote>
<p>Note: The concept of friend is used here as in plain language. Nothing to do with the keyword <em>friend</em> in c++</p>
</blockquote>
<h3 id="keeping-the-law">Keeping the Law</h3>
<p>For a better maintenance, it’s better to rewrite the code to abstract <em>Wallet</em> away, so <em>Customer</em> offers 2 methods:</p>
<pre><code>HowMuchMoney
Pay
</code></pre>
<p>while the <em>Wallet</em> itself remains unexposed.</p>
<pre class=" language-c"><code class="prism ++ language-c">class Customer <span class="token punctuation">{</span>

public<span class="token punctuation">:</span>
    <span class="token function">Customer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        myWallet <span class="token operator">=</span> new <span class="token function">Wallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        myWallet<span class="token operator">-&gt;</span><span class="token function">addMoney</span><span class="token punctuation">(</span><span class="token number">200</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token operator">~</span><span class="token function">Customer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>  delete myWallet<span class="token punctuation">;</span> <span class="token punctuation">}</span>

<span class="token keyword">const</span> <span class="token keyword">int</span> <span class="token function">howMuchMoney</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> myWallet<span class="token operator">-&gt;</span><span class="token function">howMuchMoney</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">int</span> <span class="token function">pay</span><span class="token punctuation">(</span><span class="token keyword">int</span> amount<span class="token punctuation">)</span> <span class="token punctuation">{</span>
 <span class="token keyword">return</span> myWallet<span class="token operator">-&gt;</span><span class="token function">retrieveMoney</span><span class="token punctuation">(</span>amount<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

private<span class="token punctuation">:</span>
    Wallet<span class="token operator">*</span> myWallet<span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>

class Wallet 
<span class="token punctuation">{</span>
public<span class="token punctuation">:</span>
    <span class="token function">Wallet</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
    <span class="token punctuation">:</span><span class="token function">currentMoney</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span><span class="token punctuation">}</span>

    <span class="token keyword">void</span> <span class="token function">addMoney</span><span class="token punctuation">(</span><span class="token keyword">int</span> amount<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        currentMoney <span class="token operator">+</span><span class="token operator">=</span> amount<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">const</span> <span class="token keyword">int</span> <span class="token function">howMuchMoney</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> currentMoney<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">int</span> <span class="token function">retrieveMoney</span><span class="token punctuation">(</span><span class="token keyword">int</span> amount<span class="token punctuation">)</span> <span class="token punctuation">{</span>
       <span class="token keyword">int</span> moneyTaken<span class="token punctuation">;</span>
       <span class="token keyword">if</span> <span class="token punctuation">(</span>amount <span class="token operator">&lt;=</span> currentMoney<span class="token punctuation">)</span> <span class="token punctuation">{</span>
           moneyTaken <span class="token operator">=</span> amount<span class="token punctuation">;</span>
           currentMoney <span class="token operator">-</span><span class="token operator">=</span> amount<span class="token punctuation">;</span>
       <span class="token punctuation">}</span>
       <span class="token keyword">else</span> <span class="token punctuation">{</span>
           moneyTaken <span class="token operator">=</span> currentMoney<span class="token punctuation">;</span>
           currentMoney <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>
       <span class="token punctuation">}</span>

       <span class="token keyword">return</span> moneyTaken<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

private<span class="token punctuation">:</span>

    <span class="token keyword">int</span> currentMoney<span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>

</code></pre>
<h3 id="why-is-this-way-better">Why is this way better?</h3>
<p>You can argue that we added more complexity in <em>Customer</em> class and some method of Customer class are just wrapper of <em>Wallet</em>’s ones. Yes, that the price of abstraction. In return, you get</p>
<ul>
<li>Code easier to understand: the Customer clients do not need to know about the relationship between Customer and Wallet.</li>
<li>Better maintainability: If the customer decides to change the Wallet for a payment App, the customer’s client remain unaffected. also changes in the interface of the Wallet does not propagate to the clients of Customer.</li>
</ul>
<p>But at the end, this models better the relationship in the real world. Do you handover your wallet to the shop assistant at the market when you buy your stuff? I bet you don’t. Instead you handle your wallet by yourself. It’s more <em>complex</em>, as you have to take the cash,  check the change, and so on, but for all of us, this effort pays off.</p>
<p>So it should be in software design.</p>
<h4 id="references">References</h4>
<p>Original paper: <a href="http://www.ccs.neu.edu/research/demeter/papers/law-of-demeter/oopsla88-law-of-demeter.pdf">http://www.ccs.neu.edu/research/demeter/papers/law-of-demeter/oopsla88-law-of-demeter.pdf</a><br>
“The Paperboy, The Wallet,and The Law Of Demeter” . D Bock<br>
“An Empirical Validation of the Benefits of Adhering to the Law of Demeter”.Guo, Würsch, Giger and Gall.<br>
<a href="https://en.wikipedia.org/wiki/Law_of_Demeter">https://en.wikipedia.org/wiki/Law_of_Demeter</a><br>
<a href="https://pragprog.com/articles/tell-dont-ask">https://pragprog.com/articles/tell-dont-ask</a></p>

