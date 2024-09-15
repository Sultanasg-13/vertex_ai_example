# vertex_ai_exaplme

The following are the steps to test the deployed model by sending a PDF file for summerization

## Step 1: Install necessary libraries for PDF text extraction
`!pip install PyPDF2 google-cloud-aiplatform google-cloud-storage`


## Step 2: Connet to the Model's endpoint
```
# prompt: connect to a model's endpoint

from google.cloud import aiplatform

# TODO(developer): Replace these values
ENDPOINT_NAME = "projects/{Project_ID}/locations/{Region}/endpoints/{Endpoint_ID}"

endpoint = aiplatform.Endpoint(endpoint_name=ENDPOINT_NAME)
```


## Step 3: Test endpoint connection
```
# prompt: send a prompt to test the model

response = endpoint.predict(
    [
        {
            "prompt": "ما هي عاصمة السعودية"
        }
    ]
)
print(response.predictions[0])
```

## Step 4: Extract text from the PDF file using pyPDF2:
```
import PyPDF2

def extract_text_from_pdf(pdf_path):
    """ Extracts text from a PDF file."""
    with open(pdf_path, 'rb') as file:
        reader = PyPDF2.PdfReader(file)
        text = ''
        for page in reader.pages:
            text += page.extract_text()
    return text

# Example usage:
pdf_path = 'path-to-your-local-file.pdf'
pdf_text = extract_text_from_pdf(pdf_path)

```


## Step 5: Clean the text before sending the prompt
```
import re

def clean_text(text):
    # Remove extra whitespace, line breaks, etc.
    text = re.sub(r'\s+', ' ', text)
    return text.strip()

pdf_text = clean_text(pdf_text)
print(f"Cleaned text: {pdf_text}")
```

## Step 6: Send prompt
```

# Prepare the input data, using the extracted text in the 'prompt' field
instances = [{'prompt': "summarize the following text: "+pdf_text}]

# Call the Llama model with the text input
response = endpoint.predict(instances=instances)

# Print the prediction result
print(response.predictions[0])
```

## Step 7: Clean up
```
endpoint.undeploy_all()
```
