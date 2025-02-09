<div align="center">
    <a href="https://luolingchun.github.io/flask-openapi3/" target="_blank">
        <img class="off-glb" src="https://github.com/luolingchun/flask-openapi3/raw/master/docs/images/logo-text.png" width="50%"
             height="auto" alt="logo">
    </a>
</div>
<p align="center">
    <em>为你的 Flask 项目生成 REST API 和 OpenAPI 文档。</em>
</p>
<p align="center">
    <a href="https://github.com/luolingchun/flask-openapi3/actions/workflows/test.yml" target="_blank">
        <img class="off-glb" src="https://img.shields.io/github/workflow/status/luolingchun/flask-openapi3/test" alt="test">
    </a>
    <a href="https://pypi.org/project/flask-openapi3/" target="_blank">
        <img class="off-glb" src="https://img.shields.io/pypi/v/flask-openapi3" alt="pypi">
    </a>
    <a href="https://pypistats.org/packages/flask-openapi3" target="_blank">
        <img class="off-glb" src="https://img.shields.io/pypi/dm/flask-openapi3" alt="pypistats">
    </a>
    <a href="https://pypi.org/project/flask-openapi3/" target="_blank">
        <img class="off-glb" src="https://img.shields.io/pypi/pyversions/flask-openapi3" alt="pypi versions">
    </a>
</p>

**Flask OpenAPI3** 是一个基于 **Flask** 的 web API 框架，使用 **Pydantic** 验证数据，自动生成 **Swagger**，**ReDoc** 和 **RapiDoc**
交互文档。

主要特点有：

- **编码简单：** 使用简单易于学习
- **标准的文档规范：** 基于[开放 API 规范](https://github.com/OAI/OpenAPI-Specification)
- **交互式 OpenAPI 文档：** [Swagger](https://github.com/swagger-api/swagger-ui), [Redoc](https://github.com/Redocly/redoc)
  和 [RapiDoc](https://github.com/mrin9/RapiDoc)
- **数据验证：** 基于 [Pydantic](https://github.com/samuelcolvin/pydantic) 的快速数据验证
- **认证：** 支持在 Swagger UI 中重新加载认证信息

## 依赖

Python 3.7+

flask-openapi3 依赖以下库：

- [Flask](https://github.com/pallets/flask)：用于WEB服务
- [Pydantic](https://github.com/samuelcolvin/pydantic)：用于数据验证

## 安装

```bash
pip install -U flask-openapi3
```

## 一个简单的示例

这里有一个简单的示例，更多示例请查看[示例](https://luolingchun.github.io/flask-openapi3/zh/Example/)。

```python
from pydantic import BaseModel

from flask_openapi3 import Info, Tag
from flask_openapi3 import OpenAPI

info = Info(title="book API", version="1.0.0")
app = OpenAPI(__name__, info=info)

book_tag = Tag(name="book", description="Some Book")


class BookQuery(BaseModel):
    age: int
    author: str


@app.get("/book", tags=[book_tag])
def get_book(query: BookQuery):
    """get books
    get all books
    """
    return {
        "code": 0,
        "message": "ok",
        "data": [
            {"bid": 1, "age": query.age, "author": query.author},
            {"bid": 2, "age": query.age, "author": query.author}
        ]
    }


if __name__ == "__main__":
    app.run(debug=True)
```


<details>
<summary>基于类的 API 视图示例</summary>

```python
from typing import Optional

from pydantic import BaseModel, Field

from flask_openapi3 import OpenAPI, Tag, Info, APIView


info = Info(title='book API', version='1.0.0')
app = OpenAPI(__name__, info=info)

api_view = APIView(url_prefix="/api/v1", view_tags=[Tag(name="book")])


class BookPath(BaseModel):
    id: int = Field(..., description="book ID")


class BookQuery(BaseModel):
    age: Optional[int] = Field(None, description='Age')


class BookBody(BaseModel):
    age: Optional[int] = Field(..., ge=2, le=4, description='Age')
    author: str = Field(None, min_length=2, max_length=4, description='Author')


@api_view.route("/book")
class BookListAPIView:
    a = 1

    @api_view.doc(summary="get book list")
    def get(self, query: BookQuery):
        print(self.a)
        return query.json()

    @api_view.doc(summary="create book")
    def post(self, body: BookBody):
        """description for create book"""
        return body.json()


@api_view.route("/book/<id>")
class BookAPIView:
    @api_view.doc(summary="get book")
    def get(self, path: BookPath):
        print(path)
        return "get"

    @api_view.doc(summary="update book")
    def put(self, path: BookPath):
        print(path)
        return "put"

    @api_view.doc(summary="delete book", deprecated=True)
    def delete(self, path: BookPath):
        print(path)
        return "delete"


app.register_api_view(api_view)

if __name__ == "__main__":
    app.run(debug=True)
```
</details>

## API 文档

运行[简单示例](https://github.com/luolingchun/flask-openapi3/blob/master/examples/simple_demo.py)，然后访问
http://127.0.0.1:5000/openapi。

你将看到文档入口：[Swagger](https://github.com/swagger-api/swagger-ui)，
[Redoc](https://github.com/Redocly/redoc) 和 [RapiDoc](https://github.com/mrin9/RapiDoc)。

![openapi](https://github.com/luolingchun/flask-openapi3/raw/master/docs/images/openapi.png)
![openapi-swagger](https://github.com/luolingchun/flask-openapi3/raw/master/docs/images/openapi-swagger.png)
![openapi-redoc](https://github.com/luolingchun/flask-openapi3/raw/master/docs/images/openapi-redoc.png)
![openapi-RapiDoc](https://github.com/luolingchun/flask-openapi3/raw/master/docs/images/openapi-rapidoc.png)
