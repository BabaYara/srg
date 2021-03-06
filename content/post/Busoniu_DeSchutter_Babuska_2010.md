---
'date': '2016-04-12'
'title': 'Busoniu, DeSchutter, Babuska(2010) (Approximate dynamic programming and
  reinforcement learning)'
---

<p>This paper provides an overview of various solution concepts that belong to the families of approximate dynamic programming and reinforcement learning.</p>
<p>I will discuss an alternative representation of a dynamic programming that is often used in this literature and provide an overview of some solution methods that are common with this representation.</p>
<h2 id="basics-q-functions">Basics: Q-functions</h2>
<p>In economics, the standard representation of a dynamic programming problem is to define a recursive function <span class="math inline"><em>V</em></span> that maps from a state space into the real line. Today I will call <span class="math inline"><em>V</em></span> the <span class="math inline"><em>V</em></span> function. The <span class="math inline"><em>V</em></span> function describes the value of being in a particular state.</p>
<p>Consider an alternative function <span class="math inline"><em>Q</em></span> that maps from the state-action space, into the real line. I will call this the <span class="math inline"><em>Q</em></span>-function. The <span class="math inline"><em>Q</em></span> function describes the value of being in a particular state and choosing a particular action.</p>
<p>As we do with the <span class="math inline"><em>V</em></span>-function, we can express the <span class="math inline"><em>Q</em></span>-function recursively using Bellman's principle of optimality. The recursive form of the <span class="math inline"><em>Q</em></span>-function is</p>
<p><br /><span class="math display"><em>Q</em>(<em>s</em>, <em>x</em>)=<em>E</em><sub><em>s</em>′∼<em>f</em>(<em>s</em>, <em>x</em>, ⋅)</sub>[<em>r</em>(<em>s</em>,<em>x</em>,<em>s</em>′)+<em>β</em>max<sub><em>x</em>′</sub><em>Q</em>(<em>s</em>′,<em>x</em>′)]</span><br /></p>
<p>We can make some basic comparisons between <span class="math inline"><em>Q</em></span> and <span class="math inline"><em>V</em></span> by studying their recursive representations:</p>
<ul>
<li>Parameteric representations (i.e. splines or other interpolands) of <span class="math inline"><em>Q</em></span> are more expensive than representations of <span class="math inline"><em>V</em></span> because <span class="math inline"><em>Q</em></span> is defined over the state-action space instead of just the state space.</li>
<li>Computing the maximization on the right hand side of <span class="math inline"><em>Q</em></span> is easier than the max on the RHS of <span class="math inline"><em>V</em></span>. This is because the expectation is <em>outside</em> the max in <span class="math inline"><em>Q</em></span>, but inside the max for <span class="math inline"><em>V</em></span>.</li>
</ul>
<p>The Bellman operator associated with the <span class="math inline"><em>Q</em></span> function is a contraction mapping under the same conditions that Bellman operator associated <span class="math inline"><em>V</em></span> is a contraction mapping. When <span class="math inline"><em>Q</em></span>'s operator is a contraction mapping, the operators' unique fixed point <span class="math inline"><em>Q</em><sup>*</sup></span> is the optimal <span class="math inline"><em>Q</em></span> function.</p>
<p>The optimal policy can be easily computed from <span class="math inline"><em>Q</em><sup>*</sup></span>: <span class="math inline">$h^* (s) = \underset{x}{\text{argmax}} Q^* (s, x)$</span></p>
<p>The optimal <span class="math inline"><em>V</em></span>-function can be obtained from <span class="math inline"><em>Q</em><sup>*</sup></span>: <span class="math inline">$V^* = \underset{x}{\max} Q^* (s, x)$</span>.</p>
<h2 id="computing-q">Computing Q</h2>
<p>We see that if we can find <span class="math inline"><em>Q</em><sup>*</sup></span>, we can get the value and policy functions we care about as economists. I want to briefly describe a few flavors of algorithms that can be used to compute <span class="math inline"><em>Q</em><sup>*</sup></span></p>
<h3 id="q-learning">Q-learning</h3>
<p>Q-learning is classified as an online, model-free algorithm. This means you can update your guess for <span class="math inline"><em>Q</em><sup>*</sup></span> whenever new data becomes available. Economists would classify this as a simulation algorithm.</p>
<p>Here's the basics:</p>
<ul>
<li>Start with any initial guess for the <span class="math inline"><em>Q</em></span> function</li>
<li>Then on each iteration <span class="math inline"><em>k</em></span>:
<ul>
<li>Given the state action pair on the <span class="math inline"><em>i</em></span>th iteration (<span class="math inline">(<em>s</em><sub><em>i</em></sub>, <em>x</em><sub><em>i</em></sub>)</span>), the observation of the period <span class="math inline"><em>i</em></span> return <span class="math inline"><em>r</em><sub><em>i</em></sub></span>, and the state iteration <span class="math inline"><em>i</em> + 1</span> <span class="math inline"><em>s</em><sub><em>i</em> + 1</sub></span>...</li>
<li>... update your guess for <span class="math inline"><em>Q</em></span> using: <br /><span class="math display"><em>Q</em><sub><em>i</em> + 1</sub>(<em>s</em><sub><em>i</em></sub>, <em>x</em><sub><em>i</em></sub>)=(1 − <em>α</em><sub><em>i</em></sub>)<em>Q</em><sub><em>i</em></sub>(<em>s</em><sub><em>i</em></sub>, <em>x</em><sub><em>i</em></sub>)+<em>α</em><sub><em>i</em></sub>(<em>r</em><sub><em>i</em></sub>+<em>β</em>max<sub><em>x</em>′</sub><em>Q</em><sub><em>i</em></sub>(<em>s</em><sub><em>i</em> + 1</sub>,<em>u</em>′))</span><br /></li>
</ul></li>
</ul>
<p>Notice that the right hand side is a convex combination of the current guess of the <span class="math inline"><em>Q</em></span> function and the right hand side of the <span class="math inline"><em>Q</em></span>-function operator. This is very similar to standard value function iteration, but has the main advantage of not having any expectations.</p>
<p>This can be applied to any model for which you have a transition function that defines the state today as a function of the state and action yesterday and a random innovation from a distribution you can sample. Many economic models fit into this framework.</p>
<p>When the action space is continuous, you need do a modest alteration of the basic algorithm to ensure asymptotic convergence to <span class="math inline"><em>Q</em><sup>*</sup></span>.</p>
<h3 id="approximate-q-iteration">Approximate Q-iteration</h3>
<p>One strength of this literature is the number of different ways of reducing the dimensionality of a dynamic programming problem.</p>
<p>Approximate Q-iteration is one example of this type of algorithm. Here instead of operating on the <span class="math inline"><em>Q</em></span>-function directly, we instead operate on a parameterized approximation of the <span class="math inline"><em>Q</em></span>-function.</p>
<p>This can be done using linear dependence on coefficients (splines, Chebyshev polynomials) or non-linear parametric approximations (neural nets).</p>
<p>We'll focus on the linearly parametrized version here. We will form an approximation <span class="math inline">$\hat{Q}(s, x) = \sum_{l=1}^n \phi_l(s, x) \theta_l = \Phi\theta$</span>.</p>
<p>We and talk about a variant of the Q-learning we previously discussed that uses gradient information to update the coefficient vector.</p>
<p>Here's the basic idea:</p>
<ul>
<li>Start with any guess of coefficients <span class="math inline"><em>θ</em></span></li>
<li>Then on each iteration <span class="math inline"><em>k</em></span>:
<ul>
<li>Given the state action pair on the <span class="math inline"><em>i</em></span>th iteration (<span class="math inline">(<em>s</em><sub><em>i</em></sub>, <em>x</em><sub><em>i</em></sub>)</span>), the observation of the period <span class="math inline"><em>i</em></span> return <span class="math inline"><em>r</em><sub><em>i</em></sub></span>, and the state iteration <span class="math inline"><em>i</em> + 1</span> <span class="math inline"><em>s</em><sub><em>i</em> + 1</sub></span>...</li>
<li>... update your guess for <span class="math inline"><em>θ</em></span> using: <br /><span class="math display">$$\theta_{i+1} = \theta_i + \alpha_i \left[r_i + \beta \max_{x'} \hat{Q}_i(s_{i+1}, x')  - \hat{Q}_i(s_i, x_i)\right] \frac{\partial}{\partial \theta_i} \hat{Q}_i(s_i, x_i)$$</span><br /></li>
<li>In the linear parameterization world we are in this becomes <br /><span class="math display"><em>θ</em><sub><em>i</em> + 1</sub> = <em>θ</em><sub><em>i</em></sub> + <em>α</em><sub><em>i</em></sub>[<em>r</em><sub><em>i</em></sub>+<em>β</em>max<sub><em>x</em>′</sub>(<em>Φ</em>(<em>s</em><sub><em>i</em> + 1</sub>,<em>x</em>′)<em>θ</em><sub><em>i</em></sub>)−<em>Φ</em>(<em>s</em><sub><em>i</em></sub>,<em>x</em><sub><em>i</em></sub>)<em>θ</em><sub><em>i</em></sub>]<em>Φ</em>(<em>s</em><sub><em>i</em></sub>, <em>x</em><sub><em>i</em></sub>)</span><br /></li>
</ul></li>
</ul>
<p>As this is a Q-learning algorithm, the same computational benefits arise here, but now we additionally have the benefit of a potentially vastly reduced computational problem depending on the state-action space the choice of basis functions.</p>

