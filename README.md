Sample notebok from [LangChain: Chat with Your Data](https://learn.deeplearning.ai/langchain-chat-with-your-data) using Redis as vectorstore for RAG and local LLM and embeddings.


* Other than the python, jupyter you will need. 
	
	* Redis Stack `docker run -it --name redis-stack -p 6379:6379 -p 8001:8001  --rm redis/redis-stack:7.2.0-v0`
	* LLM model
	* Inference Engine
	
	If you are new to Jupyter etc some commands below
	
```
  python3.10 -m venv venv
	. ./venv/bin/activate
  pip install notebook
  jupyter notebook
```

  ### LLM Model
  
  Meta have releases  [Llama-2](https://ai.meta.com/llama/) model. Running the models require [GPU and memory](https://finbarr.ca/how-is-llama-cpp-possible/) and APPL M1/M2 for consumers is the most convenient option to run a local model. You can download the models from HuggingFace. I used the follwong models. 
	
  
  | Model                                                        | Notes                                                        |
  | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | [llama-2-7b-chat.ggmlv3.q6_K.bin](https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGML/blob/main/llama-2-7b-chat.ggmlv3.q6_K.bin) | If you do not have a powerful GPU or 8GB RAM (M1 Max performance ~30 tokens/second). This is the smallest llama-2 model |
  | [llama-2-13b-chat.ggmlv3.q6_K.bin](https://huggingface.co/TheBloke/Llama-2-13B-chat-GGML/blob/main/llama-2-13b-chat.ggmlv3.q6_K.bin) | 16GB RAM and GPU (M1 Max performance ~16  tokens/second)     
  
  ### <a name="inference">Inference Engine</a>
  
  * [llama2.cpp](https://github.com/ggerganov/llama.cpp). The inference engine used by every  other tool. To Run as open api server see [server example](https://github.com/ggerganov/llama.cpp/blob/master/examples/server/README.md). I used the following commands to compile and run the server
  ```
  LLAMA_METAL=1 make
  
  # Run the server
  ./server  -m ~/.cache/lm-studio/models/thebloke/Llama-2-70B-Chat-GGML/llama-2-70b-chat.ggmlv3.q4_K_S.bin -ngl 1 -gqa 8   --ctx-size 4000  -v
  
  
  #In a separte temrinal run the open api server
  python3.10 -m venv venv
  . ./venv/bin/activate
  pip install flask
  pip install requests
  python examples/server/api_like_OAI.py
  
  # and you can test by
   curl http://localhost:8081/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      {  "role": "user", "content":"Tell me about the capabilities of redis enterprise and when to use it" }
    ],
    "temperature": 0.2,
    "max_tokens": 100,
    "stream": false
  }'
  ```
