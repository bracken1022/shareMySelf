1.有时候会碰到下面的莫名其妙的错误，主要是在mac上。

rake aborted!
/Users/bracken/product/rails_agile/rails_agile/config/initializers/session_store.rb:3: syntax error, unexpected ':', expecting $end
...sion_store :cookie_store, key: '_depot_session'
                              ^

Tasks: TOP => db:migrate => environment
(See full trace by running task with --trace)

后来发现原因是ruby的版本和rails版本不匹配，mac上面默认为1.8.7，后续安装的1.9.3需要用rvm use 1.9.3来指定。

2. heroku 如果出现冲突，解决方法如下：
git push heroku master
To git@heroku.com:railsagile.git
     ! [rejected]        master -> master (non-fast-forward)
    error: failed to push some refs to 'git@heroku.com:railsagile.git'
    hint: Updates were rejected because the tip of your current branch is behind
    hint: its remote counterpart. Merge the remote changes (e.g. 'git pull')
    hint: before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.

输入下列命令即可：
    git pull heroku master
