# lern-fastapi
lerning fastapi

## install

```
python -m pip install virtualenv 
python -m venv env 
```

## Linux and macOS

```
source env/bin/activate 
```

## Windows

```
.\env\Scripts\activate
```

## install package

```
pip install fastapi
pip install uvicorn[standard]
```

## main.py
```py
#!python
import os
import uvicorn
from fastapi import FastAPI, File, UploadFile
from pydantic import BaseModel
from typing import List

app = FastAPI()

class Book(BaseModel):
    title: str
    price: float

book_db = [
    {
        "title":"The C Programming",
        "price": 720
    },
    {
        "title":"Learn Python the Hard Way",
        "price": 870
    },
    {
        "title":"JavaScript: The Definitive Guide",
        "price": 1369
    },
    {
        "title":"Python for Data Analysis",
        "price": 1394
    },
    {
        "title":"Clean Code",
        "price": 1500
    },
]

@app.get("/")
async def root():
    return {"message": "Hello World"}

@app.get("/book/")
async def get_books():
    return book_db

@app.get("/book/{book_id}")
async def get_book(book_id: int):
    return book_db[book_id-1]

@app.post("/book")
async def create_book(book: Book):
    book_db.append(book.dict())
    return book_db[-1]

# upload single file
@app.post("/img")
async def up_img_book(file: UploadFile = File(...)):
    size = await file.read()
    return  { "File Name": file.filename, "size": len(size)}

# upload multi file
@app.post("/multi-img")
async def up_multi_file(files: List[UploadFile] = File(...)):
    file = [
        {
            "File Name":file.filename, 
            "Size":len(await file.read())
        } for file in files]
    return  file

@app.put("/book/{book_id}")
async def edit_book(book_id: int, book: Book):
    result = book.dict()
    book_db[book_id-1].update(result)
    return result

@app.delete("/book/{book_id}")
async def delete_book(book_id: int):
    book = book_db.pop(book_id-1)
    return book

if __name__ == "__main__":
    uvicorn.run("main:app", host="0.0.0.0", port=8000, reload=True)
```

## run command
```sh
python main.py
```

## OR

```sh
uvicorn main:app --reload
uvicorn main:app --reload --port 8000
```

## test 

```
http://127.0.0.1:8000/docs
```
