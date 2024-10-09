# AI Paper Retrieval and Upload Script

## Introduction
This project provides a Python script that automates the retrieval of academic papers from the CORE repository based on a specific query and uploads them to a designated Google Drive folder. The script handles API rate limiting, pagination, and ensures compliance with licensing agreements. It is particularly useful for researchers conducting meta-analyses or systematic literature reviews by automating the collection of relevant full-text PDFs.

## Table of Contents
- [Introduction](#introduction)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Code Overview](#code-overview)
- [Examples](#examples)
- [Contributing](#contributing)
- [License](#license)
- [Contact Information](#contact-information)

## Features
- Retrieves academic papers from the CORE repository based on a custom query.
- Handles API rate limiting and pagination to comply with CORE API policies.
- Downloads full-text PDFs or generates PDFs from full text when necessary.
- Uploads the retrieved papers to a specified Google Drive folder.
- Avoids duplicate downloads and maintains a log of processed papers.
- Configurable parameters for query, page size, and total papers to retrieve.
- Implements exponential backoff for handling rate limits gracefully.
- Checks total available papers before processing to optimize the retrieval process.

## Prerequisites
- Python 3.9 or higher
- CORE API key
- Google Cloud project with Google Drive API enabled
- `credentials.json` file for Google Drive API authentication
- Required Python libraries:
  - `requests`
  - `google-auth-oauthlib`
  - `google-api-python-client`
  - `fpdf`
  - `tqdm`

## Installation
### Clone the repository:
```bash
git clone https://github.com/HosungYou/new_hosung.git
cd new_hosung
```

### Create a virtual environment (optional but recommended):
```bash
python -m venv venv
source venv/bin/activate  # On Windows, use 'venv\Scripts\activate'
```

### Install the required libraries:
```bash
pip install -r requirements.txt
```
If `requirements.txt` is not available, you can install the libraries directly:
```bash
pip install requests google-auth-oauthlib google-api-python-client fpdf tqdm
```

### Obtain your CORE API key:
- Sign up at [CORE API](https://core.ac.uk/services/api/).
- Replace `'YOUR_CORE_API_KEY'` in the script with your actual API key.

### Set up Google Drive API credentials:
- Create a project in the [Google Cloud Console](https://console.cloud.google.com/).
- Enable the Google Drive API for your project.
- Create OAuth 2.0 Client ID credentials:
  - Application type: Desktop app
  - Download the `credentials.json` file.
- Place the `credentials.json` file in the same directory as the script.

### Configure the script parameters:
Open the script file (e.g., `script.py`) in a text editor. Modify the following variables in the Configuration Section:
```python
# --- Configuration Section ---
CORE_API_KEY = 'YOUR_CORE_API_KEY'
SCOPES = ['https://www.googleapis.com/auth/drive.file']
QUERY = '"AI adoption" AND TOE'  # Modify your search query as needed
PAGE_SIZE = 100  # Adjust the number of results per page
DRIVE_FOLDER_ID = 'YOUR_GOOGLE_DRIVE_FOLDER_ID'  # Set to None if uploading to root
MAX_RETRIES = 5
RETRY_WAIT_TIME = 60
# --- End of Configuration Section ---
```
**Note**: `DRIVE_FOLDER_ID` can be found in the URL of your Google Drive folder. For example, in `https://drive.google.com/drive/folders/ABC123`, `ABC123` is the folder ID.

## Usage
To run the script, execute the following command in your terminal:
```bash
python script.py
```
**Example**:
```bash
python script.py
```

The script will:
- Authenticate with Google Drive.
- Retrieve the total number of available papers for your query.
- Fetch papers from the CORE repository based on your query, handling pagination and rate limits.
- Download PDFs or generate them from full text.
- Upload the papers to your specified Google Drive folder.

**Note**:
- The first time you run the script, it will open a browser window prompting you to authorize access to your Google Drive account.
- Make sure you are logged into the correct Google account with access to the specified Drive folder.

## Code Overview
The script consists of the following main functions:
- `authenticate_google_drive()`: Handles authentication with the Google Drive API using OAuth 2.0.
- `search_core(query, offset, limit, retries)`: Queries the CORE API to search for papers based on the specified query, offset, and limit, with retry logic for rate limiting.
- `get_outputs_for_work(work_id, retries)`: Retrieves the outputs (e.g., download URLs, full text) for a given work ID from the CORE API, with retry logic for rate limiting.
- `text_to_pdf(text)`: Converts plain text to a PDF file using the `fpdf` library.
- `download_and_upload_pdfs(search_results, drive_service, folder_id)`: Downloads PDFs or generates them from full text and uploads them to Google Drive.
- `get_total_available_papers(query)`: Retrieves the total number of papers available for the given query.
- `main()`: The main function that orchestrates the execution flow, handling pagination, rate limits, and avoiding duplicates.

### Rate Limiting Handling:
- The script includes logic to handle API rate limits by implementing retries with exponential backoff.
- It respects the CORE API's limit of 10 requests per minute by introducing delays between requests.
- Uses the `X-RateLimit-Retry-After` header when available to determine wait times.

### Duplicate Handling:
- Uses a set `processed_work_ids` to keep track of processed papers and avoid duplicates.
- Filters out already processed works before downloading and uploading.

### Error Handling:
- Provides informative messages for errors such as HTTP status codes, rate limit exceedance, and exceptions during downloads or uploads.
- Gracefully exits when maximum retries are exceeded or when no more results are available.

## Examples
### Sample Output:
```bash
Total available papers for query '"AI adoption" AND TOE': 150
Fetching page 1 of 2 (offset 0)...
Uploading PDFs: 100%|██████████| 100/100 [12:34<00:00,  7.54s/it]
Fetching page 2 of 2 (offset 100)...
Uploading PDFs: 100%|██████████| 50/50 [06:45<00:00,  8.10s/it]
```

### Sample PDF Uploaded to Google Drive:
- The script uploads PDFs with filenames based on the paper titles, truncated to 100 characters.
- Check your Google Drive folder to verify the uploads.
- **Example filenames**:
  - `An_Industry-Specific_Investigation_on_Artificial_Intelligence_Adoption_The_Cases_of_Financial_Servi.pdf`
  - `Exploring_artificial_intelligence_adoption_in_public_organizations_a_comparative_case_study.pdf`

### Sample Configuration Section in the Script:
```python
# --- Configuration Section ---
CORE_API_KEY = 'your_actual_core_api_key_here'
SCOPES = ['https://www.googleapis.com/auth/drive.file']
QUERY = '"AI adoption" AND TOE'
PAGE_SIZE = 100
DRIVE_FOLDER_ID = 'your_google_drive_folder_id_here'
MAX_RETRIES = 5
RETRY_WAIT_TIME = 60
# --- End of Configuration Section ---
```

## Contributing
Contributions are welcome! If you'd like to improve this script or add new features, please follow these steps:
1. **Fork the repository**.
2. **Create a new branch**:
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. **Make your changes and commit them**:
   ```bash
   git commit -m "Add your detailed description of the changes."
   ```
4. **Push to the branch**:
   ```bash
   git push origin feature/your-feature-name
   ```
5. **Submit a pull request**.

Please ensure your code follows the project's coding standards and includes appropriate documentation.

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contact Information
- **Author**: Hosung You
- **Email**: [hfy5138@psu.edu](mailto:hfy5138@psu.edu)
- **GitHub**: [HosungYou](https://github.com/HosungYou)

Feel free to reach out for any questions or suggestions!

## Additional Notes
### Ethical and Legal Compliance:
- Ensure that you have the rights to download and use the papers according to their licenses.
- The script attempts to verify the license type before downloading.
- Be aware of the CORE API terms of service and comply accordingly.

### API Rate Limits:
- The CORE API allows up to 10,000 tokens per day and 10 tokens per minute for registered users.
- The script includes delays and retry logic to handle these limits.

### Storage Considerations:
- Downloading a large number of PDFs can consume significant storage space.
- Ensure you have enough space in your Google Drive and local machine if necessary.

### Error Handling:
- The script handles various exceptions and provides informative messages.
- If you encounter issues, check the output messages for guidance.

### Acknowledgments:
This project utilizes the following libraries and services:
- [CORE API](https://core.ac.uk/services/api/)
- [Google Drive API](https://developers.google.com/drive)
- [FPDF for Python](http://www.fpdf.org/)
- [tqdm for progress bars](https://github.com/tqdm/tqdm)
