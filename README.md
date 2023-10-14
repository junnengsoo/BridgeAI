# BridgeAI
The following implementation is a proof of concept of BridgeAI which utilises a pipeline that includes OpenAI's text embedding model and gpt3.5. LangChain is utilised to allow for semantic search of a vectorstore consisting of a bot's chat database.

As this is only a proof of concept that utilises semantic search rather than fine-tuning of a model, hallucinations do happen and replies are not ideal.

Moving forward, we will utilise Llama-2 as the base model for fine-tuning with a bot's chat database for our minimum viable product.

## To initialize

Get the API key from OPEN AI's website and Pinecone and set it in your OS envrionment variable through the .env file

For instance : 

```
OPENAI_API_KEY=sk-****
PINECONE_API_KEY=***
PINECONE_ENV=us-****
```

Make sure you have `pip` installed in your device, it is just a package manager for python, then run

```
pip install -r requirements.txt
```

Run `aws configure` and fill in the AWS Access Key ID, AWS Secret Access Key and Region.

Navigate to the  directory and simply run :
```
flask run
```

You can comment out certain parts of the `print` statement in the code and remove the `verbose=True` property in the `ConversationalRetrievalChain` if you do not wish to see so much logging that is cluttering your CLI.

The `ingestion-template.txt` in the root directory is the sample txt file that one can specify when calling the `ingest/template` API. Do not that each key (i.e user,bot,entities etc) should be in its own line.

**API foot print** : 

**POST** `/startchat?user={user}&bot={bot}`
This API will initialize a conversation chain between a user and a bot. This API must be triggered before any `chat` API call.
`localhost:5000/startchat?user=mitch&bot=fabian`

**POST** `/chat?user={user}&bot={bot}` 
This API will send a message to chat with the bot after the conversation has been initialized.
`localhost:5000/chat?user=mitch&bot=fabian`
request body : 
```
{
  "message" : "Hello, how are you feeling?"
}
```

sample response : 
```
{
  "Message": "Hey feebi! I'm feeling alright, just a bit tired. How about you?",
  "Status": "success"
}
```

**POST** `/save?user={user}&bot={bot}` 
This API will save all the chat history between the bot and the user so far and upload it onto S3 buckets.
`localhost:5000/save?user=mitch&bot=fabian`



**POST** `/ingest?index={indexName}&bot={bot}`
This API will take in a file form and upload the file onto Pinecone index and namespace of {bot}

file form : 
-file name : "file", 

sample response : 
```
File successfully uploaded.
```

**POST** `ingest/template?bot={bot}`
This API will take in a file form of ingestion template and upload the file onto S3 bucket named `digital-immortality-ingestion-template` and path of `/{bot}`

file form : 
-file name : "file", 

sample response : 
```
File successfully uploaded.
```