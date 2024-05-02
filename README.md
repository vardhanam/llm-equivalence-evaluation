# LLM Equivalence Evaluation

This code performs an equivalence evaluation between human and bot's responses based on a given conversation history. It processes a CSV file containing the conversation history and user's final response and uses the OpenAI API to compare if the human response is equivalent to the final question posed by the bot.

## Prerequisites

- Python 3.x
- pandas library
- openai library
- A valid OpenAI API token

## Setup

1. Install the required libraries:
   ```
   pip install -r requirements.txt
   ```

2. Set your OpenAI API token as an environment variable:
   ```python
   os.environ['OPENAI_API_KEY'] = 'YOUR_API_TOKEN'
   ```

3. Update the `file_path` variable with the path to your CSV file.

## Code Flow for app.ipynb

1. The code starts by importing the necessary libraries: `pandas`, `os`, `openai`, `ast`, and `time`.

2. It loads the CSV file specified by `file_path` into a pandas DataFrame called `df`.

3. The `get_text_from_image_url` function is defined to extract text from an image URL using the OpenAI API. It sends a request to the API with the image URL and returns the extracted text. It is used whenever the user has submitted images in the conversation, to get information on those images.

4. The `get_prompt` function is defined to generate a prompt for the LLM based on the conversation history and user response. It takes the conversation history and user response as input and returns a formatted prompt string.

The `get_prompt` function is a crucial component of the LLM Equivalence Evaluation code. It takes the conversation history and user response as input and generates a well-structured prompt for the LLM.

The function starts by initializing a prompt string with instructions for the LLM on how to evaluate the equivalence between the user response and the correct answer. It then processes the conversation history, which is provided as a string representation of a list of dictionaries.

The function iterates over each message in the conversation history, extracting user and bot messages. If the user message contains an image URL, it uses the `get_text_from_image_url` function to extract the text from the image and includes it in the prompt. Finally, the function appends the final question and the user's response to the prompt string and returns the generated prompt.

The `get_prompt` function plays a vital role in providing the necessary context for the LLM to perform the equivalence evaluation accurately.

5. The `process_dataframe` function is defined to apply the `get_prompt` function to each row of the DataFrame. It generates a prompt for each row and adds a new column called 'Prompt' to the DataFrame.

6. The `get_llm_response` function is defined to send the prompt to the OpenAI API and retrieve the LLM response. It also calculates the time taken and cost for each request.

7. The `process_llm_response` function is defined to apply the `get_llm_response` function to each row of the DataFrame. It adds three new columns to the DataFrame: 'LLM Equivalence Evaluation (Response)', 'Time taken to complete the request', and 'Cost in dollars for the request'.

8. The `evaluate_equivalence` function is defined to compare the human evaluation and LLM response for each row. It determines if the row is a True Positive (both evaluations are EQUIVALENT), True Negative (both evaluations are NOT_EQUIVALENT), or a Mismatch (evaluations differ).

9. The code applies the `evaluate_equivalence` function to each row of the DataFrame and adds a new column called 'Evaluation Result'.

10. The final dataframe is saved in the final_results.csv


## Usage

1. Ensure that you have the required libraries installed and have set your OpenAI API token.

2. Update the `file_path` variable with the path to your CSV file.

3. Run the code.

4. The code will process the CSV file, generate prompts, retrieve LLM responses, evaluate equivalence, and save the results to a new CSV file named 'final_results.csv'.

Note: The code assumes that the input CSV file has columns named 'Conversation History', 'User Response', and 'Human Evaluation'. Adjust the column names in the code if necessary to match your CSV file.

## Using Open-Source LLMs

In addition to using the OpenAI API, you can also utilize open-source LLMs like `ollama` if you don't have an OpenAI API key. Here's an example of how you can use `llama3` for generating LLM responses:

```python
from ollama import Client

client = Client(host='http://localhost:11434')
response = client.chat(model='llama3', messages=[
    {
        'role': 'user',
        'content': prompt,
    },
])
```


The `ollama` library provides a simple and convenient way to interact with open-source LLMs without the need for an OpenAI API key. It offers a range of models and customization options to suit different requirements.

For more detailed information on how to use `ollama` and its various features, you can refer to the official documentation at [https://github.com/ollama/ollama-python](https://github.com/ollama/ollama-python).

# LLM Equivalence Evaluation API

This is a Flask API (flask_app.py) that evaluates the equivalence between a user's response to bot's question and the correct answer based on a given conversation history. The API utilizes the OpenAI API to generate prompts and retrieve LLM (Language Model) responses.

## Setup

1. Clone the repository or download the code files.

2. Install the required dependencies by running the following command:
   ```
   pip install -r requirements.txt
   ```

3. Set up your OpenAI API key:
   - Replace `'YOUR_API_KEY'` in the code with your actual OpenAI API key.
   - Alternatively, you can set the `OPENAI_API_KEY` environment variable with your API key.

## Usage

1. Run the Flask application by executing the following command:
   ```
   python app.py
   ```

2. The API will start running on `http://localhost:5000`.

3. To evaluate the equivalence between a user's response and the correct answer, make a POST request to the `/evaluate` endpoint with the following JSON format:
   ```json
   {
       "conversation_history": "<string representation of conversation history>",
       "user_response": "<user's response to the final question>"
   }
   ```

4. The API will process the request and return a JSON response with the following format:
   ```json
   {
       "llm_equivalence_evaluation": "<LLM equivalence evaluation response>",
       "time_taken": <time taken to complete the request in seconds>,
       "cost": <cost in dollars for the request>
   }
   ```

   - `llm_equivalence_evaluation`: The LLM's evaluation of whether the user's response is equivalent to the correct answer. It will be either "EQUIVALENT" or "NOT_EQUIVALENT".
   - `time_taken`: The time taken (in seconds) to process the request and generate the LLM response.
   - `cost`: The cost (in dollars) for the LLM request based on the number of tokens used.

## Example

Here's an example of a mathematical conversation history and a corresponding cURL request to the API:

Conversation History:
```json
[
  {
    "user": "What is the formula for the area of a circle?",
    "bot": "The formula for the area of a circle is A = πr², where A is the area and r is the radius of the circle."
  },
  {
    "user": "How do I calculate the area if the radius is 5 units?",
    "bot": "To calculate the area of a circle with a radius of 5 units, you can plug in the value into the formula:\n\nA = π * 5²\nA = π * 25\nA ≈ 78.54 square units\n\nSo, the area of a circle with a radius of 5 units is approximately 78.54 square units."
  },
  {
    "bot": "Now, let's try a different question. If the diameter of a circle is 12 units, what is its area? (Round your answer to two decimal places.)"
  }
]
```

User Response:
```
113.10
```

cURL Request:
```bash
curl -X POST -H "Content-Type: application/json" -d '{
    "conversation_history": "[{\"user\": \"What is the formula for the area of a circle?\", \"bot\": \"The formula for the area of a circle is A = πr², where A is the area and r is the radius of the circle.\"}, {\"user\": \"How do I calculate the area if the radius is 5 units?\", \"bot\": \"To calculate the area of a circle with a radius of 5 units, you can plug in the value into the formula:\n\nA = π * 5²\nA = π * 25\nA ≈ 78.54 square units\n\nSo, the area of a circle with a radius of 5 units is approximately 78.54 square units.\"}, {\"bot\": \"Now, let''s try a different question. If the diameter of a circle is 12 units, what is its area? (Round your answer to two decimal places.)\"}]",
    "user_response": "113.10"
}' http://localhost:5000/evaluate
```

The cURL request sends the conversation history and user response to the API endpoint for evaluation. The API will process the request and return a JSON response indicating whether the user's response is equivalent to the correct answer, along with the time taken and cost for the request.

## Notes

- The conversation history should be provided as a string representation of a list of dictionaries, where each dictionary represents a message in the conversation.
- The user's response should be a string corresponding to their answer to the final question in the conversation history.
- Make sure to handle the API key securely and avoid exposing it in public repositories or client-side code.


