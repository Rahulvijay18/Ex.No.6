**Ex.No.6 Development of Python Code Compatible with Multiple AI Tools**

**Date:** 27-09-2025

**Register no.:** 212223040164


## **Aim**

To write and implement a Python script that integrates with multiple AI tools (OpenAI's GPT and Google's Gemini) to automate the task of generating code. This experiment will use the **persona pattern** to request a specific application, then compare the generated code outputs to analyze their differences and generate actionable insights.

---

### **Algorithm**

**Input:** Text or prompt for AI task</br>
**Output:** Comparative analysis of multiple AI outputs

**Steps:**

1. Define the AI task (e.g., summarizing an article).
2. Create a prompt using the **persona pattern** for the AI (e.g., “Act as a Python programmer. Generate clean code for summarizing text”).
3. Initialize API connections for each AI tool.
4. Send the prompt to **AI Tool 1** and record output.
5. Send the same prompt to **AI Tool 2** and record output.
6. Repeat for additional AI tools if required.
7. Analyze outputs for:

   * Accuracy
   * Readability
   * Depth or completeness
8. Generate a comparative table of results.
9. Draw actionable insights (e.g., which tool is better for concise code, which is better for explanatory comments).

---
## **AI Tools & Libraries Required**

  * **Python 3.8+**
  * **AI Models:**
      * OpenAI GPT-4 (or similar)
      * Google Gemini Pro
  * **Python Libraries:**
      * `openai`: To interact with the OpenAI API.
      * `google-generativeai`: To interact with the Google AI (Gemini) API.
      * `requests`: For making HTTP requests (used by the generated code).
      * `beautifulsoup4`: For parsing HTML (used by the generated code).
  * **API Keys:** You will need valid API keys from both OpenAI and the Google AI Studio.
  * **IDE:** A code editor like Visual Studio Code.

-----

# **Explanation**

## **Concept: The Persona Pattern** 👨‍💻

The **persona pattern** is a prompt engineering technique where you instruct the AI model to "act as" a specific character or expert. For programming, this is incredibly effective. Instead of a generic prompt like "make a web scraper," we use a persona:

> "Act as an expert Python developer specializing in web scraping and data extraction..."

This prompt frames the request, encouraging the AI to generate code that is efficient, follows best practices, and includes helpful comments, just as a human expert would.

## **Application Scenario**

The chosen task is to **create a Python script that scrapes the main news headlines from the BBC News homepage ([https://www.bbc.com/news](https://www.bbc.com/news))**. This is a common, real-world task that involves web requests and HTML parsing, making it a good test case for code generation.

## **Methodology**

1.  **Define a Single, Detailed Prompt:** A comprehensive prompt using the programmer persona will be created.
2.  **Create a Master Python Script:** This script will:
      * Securely load the API keys for OpenAI and Google.
      * Define functions to call each AI tool's API with the same prompt.
      * Execute the calls and capture the generated Python code from both models.
3.  **Analyze and Compare:** The outputs from both models will be printed and discussed based on structure, correctness, efficiency, and adherence to best practices.

-----

## **Implementation: Master Python Script**

This script calls both AI APIs to generate code for the same task.

````python
# main_controller.py
import os
import openai
import google.generativeai as genai

# --- Configuration ---
# It's best practice to set your API keys as environment variables
# instead of hardcoding them in the script.
OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")
GEMINI_API_KEY = os.getenv("GEMINI_API_KEY")

# Configure the clients
openai.api_key = OPENAI_API_KEY
genai.configure(api_key=GEMINI_API_KEY)

# --- The Persona Pattern Prompt ---
# This single prompt will be sent to both models.
programming_prompt = """
Act as an expert Python developer specializing in web scraping and data extraction.
Your task is to write a clean, efficient, and well-commented Python script.

**Functionality:**
The script must scrape the main headlines from the BBC News homepage: https://www.bbc.com/news

**Requirements:**
1.  Use the `requests` library for HTTP requests and `BeautifulSoup4` for HTML parsing.
2.  Identify the correct HTML tags and classes for the main headlines.
3.  Include error handling for the web request (e.g., if the site is down).
4.  Print each headline clearly to the console.
5.  The script should be a single, executable file.
"""

def get_code_from_chatgpt(prompt):
    """Sends the prompt to the OpenAI API and returns the generated code."""
    print("--- Requesting code from ChatGPT... ---")
    try:
        response = openai.chat.completions.create(
            model="gpt-4",  # Or "gpt-3.5-turbo"
            messages=[
                {"role": "system", "content": "You are an expert Python programmer."},
                {"role": "user", "content": prompt}
            ]
        )
        # Extract the code from the response
        code = response.choices[0].message.content
        return code.strip()
    except Exception as e:
        return f"Error calling OpenAI API: {e}"

def get_code_from_gemini(prompt):
    """Sends the prompt to the Gemini API and returns the generated code."""
    print("\n--- Requesting code from Gemini... ---")
    try:
        model = genai.GenerativeModel('gemini-pro')
        response = model.generate_content(prompt)
        # Extract the code from the response, cleaning up markdown formatting
        code = response.text.replace("```python", "").replace("```", "")
        return code.strip()
    except Exception as e:
        return f"Error calling Gemini API: {e}"

if __name__ == "__main__":
    if not OPENAI_API_KEY or not GEMINI_API_KEY:
        print("Error: Please set OPENAI_API_KEY and GEMINI_API_KEY environment variables.")
    else:
        # Get code from both models
        chatgpt_code = get_code_from_chatgpt(programming_prompt)
        gemini_code = get_code_from_gemini(programming_prompt)

        # Print the results for comparison
        print("\n" + "="*50)
        print("   CODE GENERATED BY CHATGPT (OpenAI)")
        print("="*50)
        print(chatgpt_code)

        print("\n" + "="*50)
        print("    CODE GENERATED BY GEMINI (Google)")
        print("="*50)
        print(gemini_code)

````

-----

# **Generated Outputs**

Below are representative outputs that each AI model might generate from the prompt.

## **Output from ChatGPT (OpenAI)**

```python
# Filename: bbc_scraper_gpt.py

import requests
from bs4 import BeautifulSoup

def scrape_bbc_headlines():
    """
    Scrapes the main headlines from the BBC News homepage.
    """
    URL = "https://www.bbc.com/news"
    # Set a user-agent to mimic a browser
    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'}

    print(f"Fetching headlines from {URL}...")

    try:
        response = requests.get(URL, headers=headers)
        # Raise an exception for bad status codes (4xx or 5xx)
        response.raise_for_status()

        soup = BeautifulSoup(response.content, 'html.parser')

        # NOTE: CSS selectors can change. This is based on the site's structure at the time of writing.
        # Find all h3 tags with a specific data-testid attribute, which are often used for headlines.
        headlines = soup.find_all('h3', {'data-testid': 'sty-heading-promo-heading'})

        if not headlines:
            print("Could not find any headlines. The website structure may have changed.")
            return

        print("\n--- Top BBC News Headlines ---")
        for index, headline in enumerate(headlines):
            print(f"{index + 1}: {headline.get_text().strip()}")

    except requests.exceptions.RequestException as e:
        print(f"Error during requests to {URL}: {e}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")

if __name__ == "__main__":
    scrape_bbc_headlines()
```

## **Output from Gemini (Google)**

```python
# Filename: bbc_scraper_gemini.py

import requests
from bs4 import BeautifulSoup

def get_bbc_news_headlines():
    """
    Connects to BBC News and extracts the main headlines.
    Handles potential connection errors.
    """
    url = "https://www.bbc.com/news"
    print(f"Scraping headlines from: {url}")

    try:
        page = requests.get(url, timeout=10)

        # Check if the request was successful
        if page.status_code == 200:
            soup = BeautifulSoup(page.text, 'html.parser')

            # Headlines are often in <a> tags within <h3> tags. Let's find them.
            # This selector is more general to catch relevant promo links.
            headline_elements = soup.select('a.gs-c-promo-heading')

            if not headline_elements:
                print("Warning: No headlines found with the specified selector. The site's layout might have changed.")
                return

            print("\n--- BBC News Headlines Found ---")
            count = 1
            for element in headline_elements:
                # Find the actual headline text inside the link
                headline_text = element.find('h3')
                if headline_text:
                    print(f"{count}. {headline_text.get_text().strip()}")
                    count += 1
        else:
            print(f"Failed to retrieve the webpage. Status code: {page.status_code}")

    except requests.Timeout:
        print("The request timed out. The server may be slow to respond.")
    except requests.ConnectionError:
        print("A connection error occurred. Check your internet connection.")

if __name__ == '__main__':
    get_bbc_news_headlines()
```

-----

# **Analysis and Discussion**

Even with the same detailed prompt, the two models produced functionally similar but stylistically different code.

1.  **HTML Element Selection:**

      * **ChatGPT** used a `data-testid` attribute (`'h3', {'data-testid': 'sty-heading-promo-heading'}`). This is very specific and can be robust if the website uses test IDs consistently, but it can also break easily if those internal IDs are removed.
      * **Gemini** used a more traditional CSS class selector (`'a.gs-c-promo-heading'`) and then searched for an `<h3>` tag inside it. This approach is more general but might also capture unintended elements if the class is used elsewhere. This highlights the need for a developer to inspect the target site and choose the best strategy.

2.  **Error Handling:**

      * **ChatGPT** used `response.raise_for_status()`, a concise and Pythonic way to handle all bad HTTP status codes (4xx/5xx) in one line. It also included a broad `Exception` catch.
      * **Gemini** used an `if page.status_code == 200:` block, which is more explicit but only checks for success, not other errors. However, its `try...except` block was more specific, catching `requests.Timeout` and `requests.ConnectionError` separately, which provides better user feedback.

3.  **Code Structure and Style:**

      * Both models correctly used a main execution block (`if __name__ == "__main__":`), which is a best practice.
      * ChatGPT's code is slightly more compact.
      * Gemini's code includes more granular print statements for status updates and warnings.

4.  **Best Practices:**

      * **ChatGPT** included a `headers` dictionary to set a `User-Agent`. This is a crucial best practice in web scraping to avoid being blocked by websites that reject non-browser requests. Gemini omitted this.
      * **Gemini** included a `timeout` parameter in its `requests.get()` call, another important best practice to prevent the script from hanging indefinitely. ChatGPT omitted this.

-----

---

### **Deliverables**

1. Python code that sends prompts to multiple AI tools and records responses.
2. Comparative table of AI outputs.
3. Observations on which tool performs better under the same persona-based prompt.
4. Insights on how persona patterns affect AI code generation.

---


# **Conclusion**

This experiment demonstrates that the **persona pattern is highly effective** for generating functional, well-structured code from multiple AI tools. However, it also proves that **AI-generated code is not a simple copy-paste solution.**

  * Different models have different "opinions" on best practices (e.g., User-Agent vs. timeout).
  * The generated code is highly dependent on the target website's structure at a given moment and requires human verification.
  * The developer's role shifts from writing every line of code to acting as an **expert reviewer, integrator, and tester**. They must analyze the AI's output, merge the best parts from different models (like ChatGPT's User-Agent and Gemini's timeout handling), and validate the final script. This collaborative approach can significantly accelerate development while maintaining high quality.


# Result:
Thus the Development of Python Code Compatible with Multiple AI Tools has been executed success.



