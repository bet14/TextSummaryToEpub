# TextSummaryToEpub

This Python script automates a pipeline that processes text files (.txt) from an input directory, sends their content to the Gemini API to generate detailed summaries in Vietnamese (over 5000 words), saves the summaries to an output directory, consolidates them into a single DOCX file, and converts the DOCX file into an EPUB format with bookmarks corresponding to each summary's title.

## Project Purpose

The script performs the following tasks:
- Traverses an input directory and its subdirectories to collect all .txt files, preserving their directory structure.
- Reads the content of each file, checks the word count (skipping files with fewer than 50 words), and sends the content to the Gemini API to generate a detailed summary in Vietnamese.
- Saves each summary to an output directory, maintaining the original directory structure, with filenames formatted as `<original_filename>_summary.txt`.
- Consolidates all summaries into a single DOCX file, where each summary is organized under a heading derived from the "Tên bài" (Article Title) line or the first non-empty line if no title is specified.
- Converts the DOCX file into an EPUB file, creating bookmarks for each summary based on its heading.

## Prerequisites

To run the script, ensure the following are available:
- **Python 3.8 or higher**: The script uses standard Python libraries and external packages.
- **Required Python packages**:
  - `requests`: For sending HTTP requests to the Gemini API.
  - `python-docx`: For creating and manipulating DOCX files.
  - `ebooklib`: For generating EPUB files.
- **Gemini API key**: A valid API key for the Gemini API, stored in a file named `Gemini API Key.txt` at the path `D:\Code\API KEY\`.
- **Input directory**: A directory containing .txt files at `D:\2025 Archive\Raw Articles txt`.
- **Output directory**: A writable directory at `D:\2025 Archive\Summaries` for storing summaries and the final DOCX and EPUB files.

## Installation

1. **Install Python**: Download and install Python 3.8 or higher from [python.org](https://www.python.org/downloads/). Verify the installation by running `python --version` in a terminal.

2. **Install dependencies**: Use pip to install the required packages. Run the following command in a terminal:
   ```bash
   pip install requests python-docx ebooklib
   ```

3. **Set up the API key**:
   - Create a text file named `Gemini API Key.txt` in the directory `D:\Code\API KEY\`.
   - Paste your Gemini API key into this file and save it. Ensure there are no extra spaces or newlines.

4. **Prepare the input directory**:
   - Place your .txt files in `D:\2025 Archive\Raw Articles txt`. The script supports files in subdirectories, and the output will mirror this structure.

## Usage

1. **Save the script**:
   - Copy the provided Python code into a file, e.g., `text_summary_to_epub.py`.
   - Ensure the script is saved in a directory accessible by your Python environment.

2. **Run the script**:
   - Open a terminal, navigate to the directory containing the script, and execute:
     ```bash
     python text_summary_to_epub.py
     ```
   - The script will:
     - Read each .txt file from the input directory.
     - Generate a summary for each file using the Gemini API.
     - Save summaries to `D:\2025 Archive\Summaries\<YYYY-MM-DD> - Summaries`, preserving subdirectory structures.
     - Create a DOCX file named `<YYYY-MM-DD>_summaries.docx` in `D:\2025 Archive\Summaries`.
     - Convert the DOCX file to an EPUB file named `<YYYY-MM-DD>_summaries.epub` in the same directory.

3. **Output details**:
   - **Summaries**: Each summary is saved as a .txt file with the format `<original_filename>_summary.txt`. The summary begins with a line starting with "Tên bài" (Article Title), followed by paragraphs of text, without bullet points or lists, as specified.
   - **DOCX file**: Contains all summaries, with each summary under a heading (level 1) derived from the "Tên bài" line or the first non-empty line. Empty lines and specific unwanted sentences (e.g., "Dưới đây là bản tóm tắt...") are removed.
   - **EPUB file**: Organizes summaries as chapters, with each chapter titled based on the DOCX heading. Bookmarks link to each chapter for easy navigation.

4. **Monitoring progress**:
   - The script prints details for each processed file, including the original word count, summary word count, and the first 100 words of the summary.
   - Errors (e.g., file not found, API failures) are logged to the console with specific messages.

## Code Structure

The script is organized into modular functions for clarity and maintainability:

- **`get_api_url(key, model_version)`**: Constructs the Gemini API URL for the specified model version (currently supports "2.5"). Raises an error for invalid versions.
- **`get_all_txt_files(root_dir)`**: Traverses the input directory to collect paths of all .txt files and their relative directory structures.
- **`generate_summary(file_path, relative_dir)`**: Processes a single .txt file by reading its content, checking the word count, sending it to the Gemini API, cleaning the response (removing asterisks and hashtags), and saving the summary to the output directory. It also handles API rotation to manage usage limits.
- **`create_docx_summary(summary_files)`**: Consolidates all summary files into a DOCX file, structuring each summary under a heading based on specific rules (e.g., prioritizing "Tên bài" lines).
- **`docx_to_epub_with_bookmarks(docx_path, epub_path)`**: Converts the DOCX file to an EPUB file, creating chapters for each heading and adding bookmarks to the table of contents.
- **`main()`**: Orchestrates the entire pipeline, calling the above functions in sequence.

## Key Features

- **API rotation**: The script alternates between API endpoints (currently both set to Gemini 2.5) after a configurable number of requests (`max_summaries_per_chat`) to avoid rate limits.
- **Directory structure preservation**: Summaries are saved in an output directory that mirrors the input directory's structure, ensuring organized storage.
- **Robust error handling**: The script catches and reports errors for file operations, API requests, and file saving, preventing crashes and providing clear feedback.
- **Flexible heading detection**: The DOCX creation process intelligently identifies headings from "Tên bài" lines, lines containing "Tên bài," or the first non-empty line, ensuring all summaries are properly organized.
- **EPUB bookmark support**: The EPUB file includes a navigable table of contents, with each summary accessible via a bookmark linked to its chapter.

## Limitations and Notes

- **API key dependency**: The script requires a valid Gemini API key. If the key is invalid or the file is missing, the script exits with an error message.
- **Fixed paths**: The input and output directory paths are hardcoded (`D:\2025 Archive\...`). Modify the script to use different paths if needed.
- **Word count filter**: Files with fewer than 50 words are skipped to avoid processing empty or trivial files.
- **API rate limits**: The script includes a 5-second delay between API requests to prevent hitting rate limits. Adjust the `time.sleep(5)` value if needed, based on your API quota.
- **Vietnamese output**: Summaries are generated in Vietnamese, as specified in the prompt sent to the Gemini API.
- **Single model version**: The script currently uses only the Gemini 2.5-flash model. To support other models, update the `model_versions` list and `get_api_url` function.

## Troubleshooting

- **File not found errors**: Ensure the API key file and input directory exist at the specified paths. Verify that the output directory is writable.
- **API errors**: Check the Gemini API key for validity and ensure your account has sufficient quota. Review error messages printed to the console for details.
- **DOCX or EPUB issues**: Verify that `python-docx` and `ebooklib` are installed correctly. Check for sufficient disk space in the output directory.
- **Empty output files**: If no DOCX or EPUB files are created, ensure that at least one input file has 50 or more words and that the API requests succeed.

## Future Improvements

- **Configurable paths**: Replace hardcoded paths with command-line arguments or a configuration file for flexibility.
- **Dynamic model selection**: Allow users to specify different Gemini model versions or other APIs via a configuration option.
- **Batch processing**: Optimize API calls by batching multiple files in a single request, if supported by the API.
- **Customizable output formats**: Add support for additional output formats, such as PDF or HTML.
- **Progress bar**: Integrate a progress bar to visually track the processing of large numbers of files.

## Contributing

Contributions are welcome! To contribute:
1. Fork the repository on GitHub.
2. Create a branch for your changes (`git checkout -b feature/your-feature`).
3. Make your modifications and test thoroughly.
4. Commit your changes with a clear message (`git commit -m "Add feature X"`).
5. Push to your fork (`git push origin feature/your-feature`).
6. Open a pull request with a detailed description of your changes.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
