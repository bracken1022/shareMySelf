
#This record from SAE

###Now i have finish the index.html which contains the login and sginin format about html. Next step i want to import the sqlalchempy for user data.

## SAE weibo_oauth 获取公共微博时间线

认证成功后，可以获取到微博的时间线：

    @view('static/view/test.tpl')
    def weibo_time_line():
    url = 'https://api.weibo.com/2/statuses/public_timeline.json' + '?access_token='+access_token
    
    f = urllib2.urlopen(url)
    s = f.read()
    weiboString = json.loads(s)
    
    if 'statuses' in weiboString:
      dictString = weiboString['statuses']

      return {'weiboDict':dictString}

上述代码中需要import jinja2_view as view
view后面跟得是模板test.tpl的路径。通过json.loads(s)将网页内容变成json数据结构。传送到test.tpl模板中。

在test.tpl模板中可以作如下设置，即可看到前50条(默认50)公共微博记录：

        <ul>
        {% for weiboInfo in weiboDict %}
        <li>
          <ul>
            {% for weibo in weiboInfo %}
              {% if weibo == 'user' %}
                <ul>
                  {% for userInfo in weiboInfo[weibo] %}
                  <li>{{userInfo}}:::::::{{weiboInfo[weibo][userInfo]}}</li>
                  {% endfor %}
                </ul>
              {% else %}
                <li>{{weibo}}::{{weiboInfo[weibo]}}</li>
              {% endif %}
            {% endfor %}
          </ul>
        </li>
        {% endfor %}
    </ul>
