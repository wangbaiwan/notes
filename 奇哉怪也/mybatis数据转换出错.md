# mybatis报错Cause: java.lang.NumberFormatException
<p>今天写代码的时候发现mybatis报错，是类型转换的错</p>

> nested exception is org.apache.ibatis.exceptions.PersistenceException: \n### Error querying database.  Cause: java.lang.NumberFormatException: For input string: \"all\"\n### Cause: java.lang.NumberFormatException: For input string: \"all\"
<p>大致意思是类型转换发生错误，无法把all字符串转换成数字。可是我并没有把对应的变量值作为一个数字在使用。上网查了资料之后，发现了问题。<br></p>

>&lt;if test="is_related != null"&gt;<br>
&nbsp;&nbsp;&lt;choose&gt;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&lt;when test="'y' == is_related"&gt;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AND bind.id IS NOT NULL<br>
&nbsp;&nbsp;&nbsp;&nbsp;&lt;/when&gt;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&lt;when test="'n' == is_related"&gt;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AND bind.id IS NULL<br>
&nbsp;&nbsp;&nbsp;&nbsp;&lt;/when&gt;<br>
&nbsp;&nbsp;&lt;/choose&gt;<br>
&lt;/if&gt;<br>
<p>当出现像'y'这种单个字符的时候，就会报错，好像mybatis会把'y'作为char而不是string，所以会把is_related转为char，就会报错。稍微修改一下</p>

>&lt;if test="is_related != null"&gt;<br>
&nbsp;&nbsp;&lt;choose&gt;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&lt;when test="'y'.toString() == is_related"&gt;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AND bind.id IS NOT NULL<br>
&nbsp;&nbsp;&nbsp;&nbsp;&lt;/when&gt;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&lt;when test="'n'.toString() == is_related"&gt;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AND bind.id IS NULL<br>
&nbsp;&nbsp;&nbsp;&nbsp;&lt;/when&gt;<br>
&nbsp;&nbsp;&lt;/choose&gt;<br>
&lt;/if&gt;<br>
<p>这样就会当做字符串了</p>
