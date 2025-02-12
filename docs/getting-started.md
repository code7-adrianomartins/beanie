# Getting started

## Installing beanie

You can simply install Beanie for the PyPi:

### PIP

```shell
pip install beanie
```

### Poetry

```shell
poetry add beanie
```

## Initialization

Getting Beanie setup in your code is really easy:

1.  Write your database model as a Pydantic class but use `beanie.Document` in place of `pydantic.BaseModel`.
2.  Initialize Motor, as Beanie uses this as an async database engine under the hood.
3.  Call `beanie.init_beanie` with the Motor client and list of Beanie models

The code below should get you started and shows of some of the field types that you can use with beanie.

```python
from typing import Optional
from pydantic import BaseModel
from beanie import Document, Indexed, init_beanie
import motor.motor_asyncio

class Category(BaseModel):
    name: str
    description: str

# This is the model that will be saved to the database
class Product(Document):
    name: str                          # You can use normal types just like in pydantic
    description: Optional[str] = None
    price: Indexed(float)              # You can also specify that a field should correspond to an index
    category: Category                 # You can include pydantic models as well

# Call this from within your event loop to get beanie setup.
async def init():
    # Create Motor client
    client = motor.motor_asyncio.AsyncIOMotorClient(
        "mongodb://user:pass@host:27017"
    )

    # Init beanie with the Product document class
    await init_beanie(client=client, database=client.db_name, document_models=[Product])

```
