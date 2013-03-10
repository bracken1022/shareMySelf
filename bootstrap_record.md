
##重新研读一下bootstrap的使用方法，依据的是bootstrap中文翻译版本。

###bootstrap提供了940px宽，12列的栅格。

###一个基本的格局由列组成，由12个等分的列组成。列还可以利用偏移来实现定位。

    <div class="row">
      <div class="span4">
      </div>
      <div class="span4 offset4">
      </div>
    </div>

###列可以进行内嵌

  列可以内嵌进其他的列中，如下的代码

     <div class="row">
      <div class="span12">
        <div class="span4">
        </div>
        <div class="span8">
        </div>
      </div>
    </div>

###row改为row-fluid就表示流式布局，即再页面中的显示不是固定像素值，而是按整个页面的百分比来的。