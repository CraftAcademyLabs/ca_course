In your views (that is, under app/views in your Rails app) you will find .html.erb files. ERB stands for Embedded RuBy. This just means that Rails processes some special tags in those files and produces HTML output to send back to the user.

There are two ERB tags that you need to remember: <%= ruby_code %> and <% ruby_code %>. Notice that the difference is the = in the first tag.

<table>
<tbody>
<tr>
<th style="width: 15%;">Tag</th>
<th style="width: 25%;">Examples</th>
<th style="width: 60%;">Description</th> </tr>
<tr>
<td>&lt;%<strong>=</strong> %&gt;</td>
<td><strong>&lt;%= @product.price %&gt;</strong></td>
<td> It runs the Ruby code and inserts the result to the HTML at that position. You can put <em>any kind of Ruby code</em> between &lt;%= and %&gt;, for instance, &lt;%= 9 * 3 %&gt; will translate to <strong>27</strong> in the page that the user is viewing. However, typically this tag is used to display some data from a model, such as the price of a product, as shown in the example here. </td> </tr>
<tr>
<td>&lt;% %&gt;</td>
<td> <strong>&lt;% if user.admin? %&gt;</strong> &lt;p&gt;Hello Admin!&lt;/p&gt; &lt;% end %&gt;</td>
<td> The Ruby code between the delimiters &lt;% and %&gt; is run but the result will <strong>not</strong> be inserted at this point in the HTML. Therefore these tags are most commonly used for control flow structures such as an if statement in the example, or loops. </td> </tr> </tbody></table>
