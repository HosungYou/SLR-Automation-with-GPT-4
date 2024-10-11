# SLR Automation with GPT-4

This repository provides a script for automating the evaluation of systematic literature review (SLR) papers using GPT-4. The script extracts text from PDF files stored in Google Drive, evaluates the content using OpenAI's GPT-4 API, and writes the evaluation results to a Google Sheet. The repository aims to streamline the process of assessing research papers according to specific quality assessment criteria.

## Table of Contents
- [Overview](#overview)
- [Setup Instructions](#setup-instructions)
  - [Environment Setup](#environment-setup)
  - [Google Cloud Configuration](#google-cloud-configuration)
  - [OpenAI API Configuration](#openai-api-configuration)
- [Components](#components)
  - [Google Services Authentication](#google-services-authentication)
  - [PDF Extraction](#pdf-extraction)
  - [GPT Evaluation](#gpt-evaluation)
  - [Google Sheets Integration](#google-sheets-integration)
- [Handling Common Errors](#handling-common-errors)
- [Changelog](#changelog)
- [Acknowledgments](#acknowledgments)

## Overview
This project builds upon the previous work in [AI Paper Retrieval and Upload Script](https://github.com/HosungYou/AI-Paper-Retrieval-and-Upload-Script), providing a sequential workflow for conducting systematic literature reviews (SLR). The initial project focuses on retrieving academic papers from the CORE repository and uploading them to Google Drive, while this repository automates the subsequent evaluation of these papers using OpenAI's GPT-4 model.

The script connects directly to a specified Google Drive folder containing PDF papers retrieved from the previous project, extracts text from each PDF, evaluates it based on SLR inclusion criteria, and records the results in a Google Sheet. The goal is to assist researchers in automating the screening and quality assessment process for SLRs in social sciences, focusing on detailed evaluation criteria while leveraging the strengths of AI-based assessments.

Users can follow the steps provided in the linked repository to collect the academic papers and then use this repository to perform automated quality assessments, making the entire SLR process more efficient and streamlined.

## Setup Instructions
### Environment Setup
1. **Clone the repository**:
   ```sh
   git clone https://github.com/HosungYou/SLR-Automation-with-GPT-4.git
   cd SLR-Automation-with-GPT-4
   ```

2. **Create a virtual environment**:
   ```sh
   python3 -m venv .venv
   source .venv/bin/activate  # On Windows use '.venv\Scripts\activate'
   ```

3. **Install dependencies**:
   ```sh
   pip install -r requirements.txt
   ```

4. **Set environment variables**:
   Create a `.env` file in the root directory and add your OpenAI API key:
   ```env
   OPENAI_API_KEY=your_openai_api_key_here
   ```

### Google Cloud Configuration
1. **Enable Google Drive and Google Sheets APIs** in the Google Cloud Console.
2. **Create a service account** and download the credentials file (`service_account.json`). Place this file in the root directory of the repository.
3. **Set Google Drive and Sheets IDs**:
   - Update the script with your Google Sheet ID and Google Drive folder ID:
     - `SHEET_ID`: The unique identifier of your Google Sheet (not the full URL).
     - `GOOGLE_DRIVE_FOLDER_ID`: The ID of the folder containing the PDFs.

### OpenAI API Configuration
1. Ensure you have access to the GPT-4 API.
2. Set the `OPENAI_API_KEY` in your `.env` file as mentioned earlier.

## Components
### Google Services Authentication
The script uses `service_account.json` to authenticate with Google services:
- **Google Drive**: Accesses PDF files in a specified folder.
- **Google Sheets**: Writes the evaluation results to a Google Sheet.
- **Code Explanation**:
  - The `authenticate_google_services` function reads the service account credentials and initializes both Google Drive and Sheets services using these credentials.

### PDF Extraction
The script uses `pdfplumber` to extract text and tables from PDF files. This step is crucial for sending data to GPT-4 for analysis.
- **Function**: `extract_text_and_tables_from_pdf` reads the PDF content and extracts both text and tables, which are later used for evaluation.
- **Code Explanation**:
  - The function uses `pdfplumber.open()` to access each page of the PDF, extracting both text and any available tables. These are then used as inputs for GPT evaluation.

### GPT Evaluation
The extracted text is evaluated using GPT-4 through the OpenAI API:
- **Environment Variable**: The OpenAI API key is securely loaded using `os.getenv('OPENAI_API_KEY')`.
- **Prompt Construction**: The script uses a structured prompt to guide GPT-4 in evaluating the paper based on Quality Assessment (QA) criteria.
- **Evaluation Function**: `evaluate_pdf_with_gpt` sends the text and tables to GPT-4 and processes the response.
- **Code Explanation**:
  - The `evaluate_pdf_with_gpt` function constructs a prompt based on the extracted text and tables, then calls `client.chat.completions.create()` to generate the evaluation.
  - **Error Handling**: The function includes a `try-except` block to handle any issues during the API call and print meaningful error messages.

### Google Sheets Integration
The script writes the evaluation results to Google Sheets using the Google Sheets API:
- **Function**: `write_data_to_google_sheets` appends the results to a specified sheet.
- **Code Explanation**:
  - The function uses `sheets_service.spreadsheets().values().append()` to add new rows of data to the Google Sheet, using options to specify how the data should be inserted.
- Ensure that the `SHEET_ID` is set to only the unique identifier, not the entire URL.

## Handling Common Errors
### 1. OpenAI 'ChatCompletion' Object Not Subscriptable
- **Error**: `'ChatCompletion' object is not subscriptable`.
- **Solution**: Ensure the response from the OpenAI API is accessed using the correct attribute-based approach:
  ```python
  evaluation = response.choices[0].message.content.strip()
  ```
  The response is a Pydantic model, and dot notation should be used for access.

### 2. Google Sheets 404 Error
- **Error**: `HttpError 404` when writing to Google Sheets.
- **Cause**: Incorrect `SHEET_ID` (full URL instead of just the ID).
- **Solution**: Use only the unique identifier for the Google Sheet.

### 3. OpenAI API Initialization
- **Issue**: Conflicting advice on API key setup.
- **Solution**: The API key should be set via environment variables for security. Ensure `.env` is used correctly, and do not hard-code the API key.
  ```python
  openai.api_key = os.getenv('OPENAI_API_KEY')
  ```

### 4. File Not Found in Google Drive
- **Error**: `No PDF files found in the specified Google Drive folder`.
- **Cause**: Incorrect folder ID or API permission issues.
- **Solution**: Double-check the `GOOGLE_DRIVE_FOLDER_ID` and ensure the service account has sufficient permissions to access the folder.

## Changelog
### Latest Update
- **Environment Variables**: Switched to `.env` file for API key management for improved security.
- **API Migration**: Updated to comply with OpenAI Python library `v1.0.0` changes.
- **Error Handling**: Improved error messages for Google Sheets and GPT evaluation issues.

## Acknowledgments
This project is inspired by the need for efficient evaluation of research papers in systematic literature reviews, leveraging AI to automate repetitive tasks and improve research workflows.
