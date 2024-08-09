# 



# 快速入门
## 模型声明


```python
from typing import List
from typing import Optional
from sqlalchemy import ForeignKey
from sqlalchemy import String
from sqlalchemy.orm import DeclarativeBase
from sqlalchemy.orm import Mapped
from sqlalchemy.orm import mapped_column
from sqlalchemy.orm import relationship

class Base(DeclarativeBase):
    pass

class User(Base):
    __tablename__ = &#34;user_account&#34;
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(30))
    fullname: Mapped[Optional[str]]
    addresses: Mapped[List[&#34;Address&#34;]] = relationship(
        back_populates=&#34;user&#34;, cascade=&#34;all, delete-orphan&#34;
    )
    def __repr__(self) -&gt; str:
        return f&#34;User(id={self.id!r}, name={self.name!r}, fullname={self.fullname!r})&#34;

class Address(Base):
    __tablename__ = &#34;address&#34;
    id: Mapped[int] = mapped_column(primary_key=True)
    email_address: Mapped[str]
    user_id: Mapped[int] = mapped_column(ForeignKey(&#34;user_account.id&#34;))
    user: Mapped[&#34;User&#34;] = relationship(back_populates=&#34;addresses&#34;)
    def __repr__(self) -&gt; str:
        return f&#34;Address(id={self.id!r}, email_address={self.email_address!r})&#34;



```


# 创建引擎
```python
from sqlalchemy import create_engine

engine = create_engine(&#34;sqlite://&#34;, echo=True)
```

# 创建表结构
```python
Base.metadata.create_all(engine)
```



# 创建对象并持久化

```python
from sqlalchemy.orm import Session

with Session(engine) as session:
    spongebob = User(
        name=&#34;spongebob&#34;,
        fullname=&#34;Spongebob Squarepants&#34;,
        addresses=[Address(email_address=&#34;spongebob@sqlalchemy.org&#34;)],
    )
    sandy = User(
        name=&#34;sandy&#34;,
        fullname=&#34;Sandy Cheeks&#34;,
        addresses=[
            Address(email_address=&#34;sandy@sqlalchemy.org&#34;),
            Address(email_address=&#34;sandy@squirrelpower.org&#34;),
        ],
    )
    patrick = User(name=&#34;patrick&#34;, fullname=&#34;Patrick Star&#34;)
    session.add_all([spongebob, sandy, patrick]) # 添加多个提交
    session.commit()
```

# 简单搜索
```python
from sqlalchemy import select

session = Session(engine)

stmt = select(User).where(User.name.in_([&#34;spongebob&#34;, &#34;sandy&#34;]))

for user in session.scalars(stmt):
    print(user)
```




---

> Author:   
> URL: https://asiaro.github.io/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/sqlalchemy-2/  

