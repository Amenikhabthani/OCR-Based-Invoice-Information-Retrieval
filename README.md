# OCR and Named Entity Recognition (NER) Pipeline

## Overview
This project extracts text from PDF documents using the Doctr OCR library and processes the extracted text using a custom Named Entity Recognition (NER) model built with spaCy. The extracted named entities are then saved to a CSV file.

## Features
- Optical Character Recognition (OCR) using Doctr.
- Named Entity Recognition (NER) using a custom-trained spaCy model.
- Saves extracted named entities in a CSV file.

## Installation
Clone the Doctr repository and install dependencies:
```bash
!git clone https://github.com/mindee/doctr.git
!pip install -q tensorflow-addons
!pip install -qe doctr/.
!pip install tf2onnx
```

## Usage

### Step 1: Perform OCR on a PDF Document
```python
from doctr.io import DocumentFile
from doctr.models import ocr_predictor

# Load the pre-trained OCR model
model = ocr_predictor(pretrained=True)

# Load and process the PDF file
invoice = DocumentFile.from_pdf("/content/BE PACK-20230162_DMT-050374.pdf")
result = model(invoice)

# Show the OCR results
result.show(invoice)

# Export extracted text to JSON
json_response = result.export()
```

### Step 2: Extract Text from JSON
```python
values = []
for page in json_response['pages']:
    for block in page['blocks']:
        for line in block['lines']:
            for word in line['words']:
                values.append(word['value'])

# Display extracted text
formatted_text = ""
for page in json_response['pages']:
    for block in page['blocks']:
        for line in block['lines']:
            for word in line['words']:
                formatted_text += word['value'] + " "
        formatted_text += "\n\n"

print(formatted_text)
```

### Step 3: Load Custom Named Entity Recognition (NER) Model
```python
import zipfile
import spacy

# Unzip the custom NER model
zip_file_path = '/content/custom_ner_model.zip'
extracted_dir_path = '/content/custom_ner_model'
with zipfile.ZipFile(zip_file_path, 'r') as zip_ref:
    zip_ref.extractall(extracted_dir_path)

# Load the spaCy model
model_path = '/content/custom_ner_model'
nlp_ner = spacy.load(model_path)
```

### Step 4: Process Extracted Text with NER Model
```python
# Process text with the NER model
doc = nlp_ner(formatted_text)

# Extract named entities
entities = [(ent.text, ent.label_) for ent in doc.ents]

# Print extracted entities
print(entities)
```

### Step 5: Save Named Entities to CSV
```python
import csv

csv_file_path = '/content/extracted_entities.csv'
with open(csv_file_path, 'w', newline='') as csv_file:
    csv_writer = csv.writer(csv_file)
    csv_writer.writerow(['Entity', 'Label'])
    for entity, label in entities:
        csv_writer.writerow([entity, label])

print(f"Extracted entities saved to {csv_file_path}")
```

## Output
- Extracted text from the PDF.
- Named entities identified by the custom NER model.
- A CSV file containing named entities and their labels.

## Requirements
- Python 3.7+
- TensorFlow
- Doctr
- spaCy
- tf2onnx
- tensorflow-addons

## License
This project follows the open-source MIT License.

## Acknowledgments
- [Doctr by Mindee](https://github.com/mindee/doctr)
- [spaCy](https://spacy.io/)

