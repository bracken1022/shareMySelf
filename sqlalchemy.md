

#SqlAlchemy 初学

参考:http://www.91python.com/archives/393

###SqlAlchemy有两种方式使用

####1.ORM

####2.


###SqlAlchemy在SAE上的使用

####1.首先在SAE上面开启Mysql服务

从我的首页进入，找到应用。在左侧兰找到服务管理->MySQL 就可以看到需要开启的选项

####2.创建数据库表

进入管理MySQL，选择操作，输入表名和属性书目。随后可以创建树形的具体内容。


####3.在代码中导入MySQL数据库相关属性

如下所示：
from sae.const import MYSQL_USER, MYSQL_PASS, MYSQL_HOST, MYSQL_PORT, MYSQL_DB

####4.添加數據庫引擎

engine = create_engine('mysql://%s:%s@%s:%s/app_forkgzb?charset=utf8' % (MYSQL_USER, MYSQL_PASS, MYSQL_HOST, MYSQL_PORT),
                            encoding='utf8', echo=False, pool_recycle=5)

metadata = MetaData(engine)
Base = declarative_base(metadata=metadata)
Session = sessionmaker(bind=engine)
session = Session()

####5.創建數據庫表

class UserInfo(Base):
  __tablename__ = 'userinfo'
  id = Column( Integer, primary_key = True )
  username = Column( String(50), nullable=False)
  password = Column( String(50), nullable=False)
  email = Column(String(50), nullable=False)

  def __init__( self, name, password, email="example@gmail.com"):
    self.name = name
    self.password = password
    self.email = email

####6.生成數據庫表

Base.metadata.create_all()