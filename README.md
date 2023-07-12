### OPENAI API SECRET KEY

1. Create .env file in main repo directory.
2. Generate Secret key on https://platform.openai.com/account/api-keys
3. Put Secret key in .env file in format: ```OPENAI_API_KEY=your_secret_key```
4. Use your Secret key to connect to Openai API:

```python 
import os
import openai

from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
openai.api_key = os.environ['OPENAI_API_KEY']
```
