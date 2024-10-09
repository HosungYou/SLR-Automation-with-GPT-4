# SLR Automation with GPT-4

## Introduction
This project provides a Python script that automates the evaluation of academic papers using OpenAI's GPT-4 model. The script connects directly to a specified Google Drive folder containing PDF papers, extracts text from each PDF, evaluates it based on systematic literature review (SLR) inclusion criteria, and records the results in a Google Sheet. This tool is designed to assist researchers in automating the screening process for SLRs in social sciences.

## Table of Contents
- [Introduction](#introduction)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Code Overview](#code-overview)
- [Limitations](#limitations)
- [Contributing](#contributing)
- [License](#license)
- [Contact Information](#contact-information)
- [Acknowledgments](#acknowledgments)
- [Disclaimer](#disclaimer)

## Features
- **Direct PDF Processing from Google Drive**: Processes PDFs directly from a specified Google Drive folder.
- **Text Extraction**: Extracts text from PDFs, focusing on the first few pages to stay within GPT-4's context window.
- **GPT-4 Evaluation**: Uses GPT-4 to assess each paper based on SLR inclusion criteria:
  - Relevance
  - Methodology Quality
  - Clarity of Findings
  - Contribution to Field
- **Results Recording**: Writes the evaluation results to a specified Google Sheet for easy review.

## Prerequisites
- **Python 3.9 or higher**
- **Google Cloud Service Account**:
  - A service account JSON key file (`service_account.json`)
  - Access to Google Drive and Google Sheets APIs
- **OpenAI API Key**: Access to the GPT-4 model via OpenAI API
- **Required Python Libraries**:
  - `openai`
  - `requests`
  - `pandas`
  - `google-auth`
  - `google-api-python-client`
  - `PyPDF2`

## Installation
1. **Clone the Repository**:
   ```bash
   git clone https://github.com/HosungYou/SLR-Automation-with-GPT4.git
   cd SLR-Automation-with-GPT4
   ```
2. **Create a Virtual Environment (Optional)**:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows, use 'venv\Scripts\activate'
   ```
3. **Install Required Libraries**:
   ```bash
   pip install -r requirements.txt
   ```
4. **Place Credentials in Project Directory**:
   - Copy the `service_account.json` file to the project directory.
   - Ensure that this file is listed in `.gitignore` to prevent accidental commits.

## Configuration
1. **Configure the Script Parameters**:
   - Open `script.py` in a text editor.
   - Modify the following variables in the Configuration Section:
     ```python
     SCOPES = ['https://www.googleapis.com/auth/spreadsheets', 'https://www.googleapis.com/auth/drive']
     SERVICE_ACCOUNT_FILE = 'service_account.json'  # Path to your Google Cloud service account credentials
     SHEET_ID = 'YOUR_GOOGLE_SHEET_ID'  # Google Sheet ID where results will be saved
     OPENAI_API_KEY = 'YOUR_OPENAI_API_KEY'  # OpenAI API key for GPT-4
     GOOGLE_DRIVE_FOLDER_ID = 'YOUR_GOOGLE_DRIVE_FOLDER_ID'  # Google Drive folder ID containing the papers
     ```
   - Replace the placeholders with your actual values.

2. **Security Considerations**:
   - Use environment variables to store sensitive data like API keys. Example:
     ```python
     import os
     OPENAI_API_KEY = os.getenv('OPENAI_API_KEY')
     ```
   - Set the environment variable before running the script:
     ```bash
     export OPENAI_API_KEY='your-api-key-here'
     ```

## Usage
Run the script using the following command:
```bash
python script.py
```
### Workflow:
1. **Authentication**: Authenticates with Google Drive and Google Sheets using service account credentials.
2. **Process PDFs**: Reads and processes each PDF, extracts text, and evaluates it using GPT-4.
3. **Record Results**: Writes evaluation results to the specified Google Sheet.

## Code Overview
- **`authenticate_google_services()`**: Authenticates with Google APIs using service account credentials.
- **`process_pdfs_from_drive(creds, folder_id)`**: Connects to Google Drive, iterates over PDF files, and evaluates them using GPT-4.
- **`extract_text_from_pdf(file_stream, max_pages=5)`**: Extracts text from a PDF file, limiting to the first `max_pages` pages.
- **`evaluate_pdf_with_gpt(text)`**: Uses GPT-4 to evaluate the extracted text based on SLR criteria.
- **`write_data_to_google_sheets(creds, sheet_id, data)`**: Writes evaluation data to a specified Google Sheet.
- **`main()`**: Orchestrates the entire workflow.

## Limitations
- **Context Length**: GPT-4 has a maximum context length. The script limits text extraction to accommodate this.
- **OpenAI API Usage**: Processing large numbers of papers may incur significant API usage and costs. Monitor your usage in the OpenAI dashboard.
- **Rate Limits**: Be mindful of OpenAI's rate limits and consider adding delays if necessary.
- **Accuracy of GPT-4 Evaluations**: GPT-4's assessments may not always align perfectly with human judgments.

## Contributing
Contributions are welcome! Please follow these steps:
1. **Fork the Repository**.
2. **Create a New Branch**:
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. **Make Your Changes and Commit**:
   ```bash
   git commit -m "Description of your changes"
   ```
4. **Push to the Branch**:
   ```bash
   git push origin feature/your-feature-name
   ```
5. **Submit a Pull Request**.

## License
This project is licensed under the MIT License. See the LICENSE file for details.

## Contact Information
- **Author**: Hosung You
- **Email**: [hfy5138@psu.edu](mailto:hfy5138@psu.edu)
- **GitHub**: [HosungYou](https://github.com/HosungYou)

Feel free to reach out with any questions or suggestions!

## Acknowledgments
- **OpenAI GPT-4**: For providing the language model used for evaluation.
- **Google Cloud Platform**: For APIs facilitating Drive and Sheets integration.
- **PyPDF2**: For PDF text extraction capabilities.

## Disclaimer
- **OpenAI Usage Policies**: Ensure compliance with OpenAI's Usage Policies when using the GPT-4 API.
- **Copyright and Privacy**: Respect copyright laws and ensure you have the right to process documents.
- **API Key Security**: Never share or expose API keys publicly. Use environment variables or secure storage to manage secrets.
