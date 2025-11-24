# AI Document Organizer

This Python script helps you organize your documents into categorized folders using a pre-trained Hugging Face zero-shot classification model. It can classify documents based on their filenames and move them into relevant categories, or flag them for manual review if confidence is low.

## Features

*   **Zero-Shot Classification**: Categorizes documents without needing prior training on your specific data, leveraging the `facebook/bart-large-mnli` model.
*   **Customizable Categories**: Easily define your own list of categories relevant to your document collection.
*   **Filename-based Classification**: Extracts a clean title from the filename for classification, removing noise like file extensions and ISBNs.
*   **Confidence Thresholding**: Documents with low classification confidence are moved to a special "Needs Manual Review" folder.
*   **Dry Run Mode**: Preview the organization structure and proposed actions before any files are moved or folders are created.
*   **Filename Filtering**: Use regular expressions to process only specific types of files (e.g., only PDF documents).
*   **GPU Acceleration**: Automatically attempts to use a CUDA-enabled GPU for faster classification if PyTorch and a compatible GPU are detected.

## Getting Started

### Prerequisites

Before running the script, ensure you have Python 3.8+ installed.

### Installation

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/dustinth2004/ai-document-organizer
    cd ai-document-organizer
    ```

2.  **Create and activate a virtual environment (recommended):**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows: .\venv\Scripts\activate
    ```

3.  **Install the package:**
    Installing the package will also install all necessary dependencies (like `transformers` and `torch`) and set up the `ai-organizer` command-line tool.
    ```bash
    pip install .
    ```
    *   **For GPU support**: If you have an NVIDIA GPU, you may need a specific version of PyTorch with CUDA support. Visit [PyTorch's official website](https://pytorch.org/get-started/locally/) to get the correct installation command before running `pip install .`.

### Usage

1.  **Place your documents** into a single folder.
2.  **Run the organizer** from your terminal, pointing it to your documents folder.

    ```bash
    ai-organizer <path_to_your_documents_folder> [OPTIONS]
    ```

    **Example (Dry Run):**
    This command will show you how files would be organized without making any changes.
    ```bash
    ai-organizer /path/to/my/documents --dry-run
    ```

    **Example (Live Run with PDF filter):**
    This command will classify and move only `.pdf` files.
    ```bash
    ai-organizer /path/to/my/documents --filter-regex ".*\.pdf$"
    ```

### Command-Line Arguments

*   `target_folder` (required): The full path to the folder containing your documents.
*   `--dry-run` (optional): A flag to preview the proposed organization without moving any files. Highly recommended for the first run.
*   `--filter-regex <pattern>` (optional): A regex pattern to filter which filenames to process (e.g., `".*\\.txt$"` for text files).

## How It Works

1.  **Initialization**: The script loads the Hugging Face zero-shot classification model, detecting and using a GPU if available.
2.  **Planning (`plan_book_organization`)**:
    *   It scans the `target_folder`, filtering files by regex if provided.
    *   For each file, it extracts a clean title by removing extensions, ISBNs, etc.
    *   It uses the model to classify the title against your `CANDIDATE_LABELS`.
    *   If the top category's confidence is below `MIN_CONFIDENCE_THRESHOLD`, the file is marked for manual review.
    *   The result is an in-memory plan of the final organization (`{category: [filenames]}`).
3.  **Execution**:
    *   **Dry Run**: Prints the plan in a tree-like format.
    *   **Live Run**: Creates the necessary category folders and moves each file to its designated folder, avoiding overwrites.

## Configuration

To customize the organizer, you can modify the constants at the top of `src/ai_document_organizer/organizer.py`:

*   `CANDIDATE_LABELS`: The list of categories for document organization.
*   `MODEL_NAME`: The Hugging Face model for classification (default: `facebook/bart-large-mnli`).
*   `MIN_CONFIDENCE_THRESHOLD`: A float (0.0-1.0) for the minimum confidence score.
*   `NEEDS_REVIEW_FOLDER_NAME`: The folder for low-confidence or error files.
*   `HYPOTHESIS_TEMPLATE`: The template used to frame the classification query.

## Contributing

Feel free to fork this repository, open issues, or submit pull requests. All contributions are welcome!