# 11. Working with existing PDFs

![enter image description here](img/undraw_file_search.png)

PDFs are everywhere—reports, contracts, research papers, invoices—you name it. But what if you need to work with a PDF programmatically? Maybe you want to extract data, analyze content, or modify a document without manually opening and editing it.

This guide will show you how to use borb to interact with existing PDFs. We'll start with simple tasks like extracting metadata and text, then move on to more advanced operations like filtering, keyword extraction, and debugging.

## Getting meta-information from a PDF

Before diving into content extraction, it’s often useful to know more about a PDF itself. Who created it? When was it last modified? What software was used to generate it? This information can help determine authenticity, version history, or even compliance requirements.

`borb` provides an easy way to retrieve this metadata:

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_11_01.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_11_01.ipynb
from borb.pdf import Document
from borb.pdf import PDF

import pathlib
import requests


def download_pdf(url: str, filename: pathlib.Path) -> None:
    """
    Downloads a PDF file from the given URL and saves it to the specified filename.

    :param url: The URL of the PDF file to download.
    :param filename: The local file path where the downloaded PDF should be saved.

    :raises requests.exceptions.RequestException: If there is an issue with the HTTP request (e.g., network failure, invalid URL, bad response).
    :raises Exception: If any other unexpected error occurs during file writing.
    """
    try:
        response = requests.get(url, stream=True)
        response.raise_for_status()  # Raise an exception for bad status codes
        with open(filename, "wb") as pdf_file:
            for chunk in response.iter_content(chunk_size=8192):
                pdf_file.write(chunk)
    except requests.exceptions.RequestException as e:
        print(f"Error downloading PDF: {e}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")


# Download
download_pdf(
    "https://github.com/borb-pdf/borb-pdf-corpus/raw/refs/heads/master/pdf/0004.pdf",
    pathlib.Path("input.pdf"),
)

# Read PDF
d: Document = PDF.read("input.pdf")

# Print meta-information
print(f"Author           : {d.get_author()}")
print(f"Creation Date    : {d.get_creation_date()}")
print(f"Modification Date: {d.get_modification_date()}")
print(f"Producer         : {d.get_producer()}")
print(f"Subject          : {d.get_subject()}")
print(f"Title            : {d.get_title()}")

```

This outputs something like:

```commandline
Author           : Mike Haskins
Creation Date    : None
Modification Date: None
Producer         : þÿMicrosoft® Word 2016
Subject          : None
Title            : None
```

## Getting text from an existing PDF

Getting the text out of a PDF is one of the most common tasks. Whether you're trying to analyze reports, extract key insights, or process invoices, automated text extraction saves time and effort.

Here’s how `borb` makes it easy:

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_11_02.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_11_02.ipynb
from borb.pdf import Document
from borb.pdf import GetText
from borb.pdf import PDF
from borb.pdf import Pipeline
from borb.pdf import Source

import pathlib
import requests


def download_pdf(url: str, filename: pathlib.Path) -> None:
    """
    Downloads a PDF file from the given URL and saves it to the specified filename.

    :param url: The URL of the PDF file to download.
    :param filename: The local file path where the downloaded PDF should be saved.

    :raises requests.exceptions.RequestException: If there is an issue with the HTTP request (e.g., network failure, invalid URL, bad response).
    :raises Exception: If any other unexpected error occurs during file writing.
    """
    try:
        response = requests.get(url, stream=True)
        response.raise_for_status()  # Raise an exception for bad status codes
        with open(filename, "wb") as pdf_file:
            for chunk in response.iter_content(chunk_size=8192):
                pdf_file.write(chunk)
    except requests.exceptions.RequestException as e:
        print(f"Error downloading PDF: {e}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")


# Download
download_pdf(
    "https://github.com/borb-pdf/borb-pdf-corpus/raw/refs/heads/master/pdf/0001.pdf",
    pathlib.Path("input.pdf"),
)

# Read PDF
d: Document = PDF.read("input.pdf")

# Process the PDF to get the text
output = Pipeline(
    [
        Source(),
        GetText(),
    ]
).process(d)

# Print the text
print(output[0])

```

This outputs:

```commandline
SAFETY      Every Step of the Way
Be Informed / Be Aware  / Be Responsive
Important Information on Working Together to Keep 
Neighbors, Communities and Pipelines Safe
```

This is great for processing large numbers of documents automatically, saving you from manually copying and pasting content.

### Filtering by font

Sometimes, not all text is equally important. Maybe you only want to extract headings, figure captions, or highlighted sections. borb lets you filter text based on font, color, or size to get exactly what you need.

For example:

- Filtering by font could be useful if headings always use a particular font style.
- Filtering by font color can help extract annotations or highlighted sections.
- Filtering by font size lets you focus on large headers while ignoring body text.

These techniques can be useful for processing documents with a structured format, like invoices or academic papers.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_11_03.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_11_03.ipynb
from borb.pdf import ByFont
from borb.pdf import Document
from borb.pdf import GetText
from borb.pdf import PDF
from borb.pdf import Pipeline
from borb.pdf import Source

import pathlib
import requests


def download_pdf(url: str, filename: pathlib.Path) -> None:
    """
    Downloads a PDF file from the given URL and saves it to the specified filename.

    :param url: The URL of the PDF file to download.
    :param filename: The local file path where the downloaded PDF should be saved.

    :raises requests.exceptions.RequestException: If there is an issue with the HTTP request (e.g., network failure, invalid URL, bad response).
    :raises Exception: If any other unexpected error occurs during file writing.
    """
    try:
        response = requests.get(url, stream=True)
        response.raise_for_status()  # Raise an exception for bad status codes
        with open(filename, "wb") as pdf_file:
            for chunk in response.iter_content(chunk_size=8192):
                pdf_file.write(chunk)
    except requests.exceptions.RequestException as e:
        print(f"Error downloading PDF: {e}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")


# Download
download_pdf(
    "https://github.com/borb-pdf/borb-pdf-corpus/raw/refs/heads/master/pdf/0001.pdf",
    pathlib.Path("input.pdf"),
)

# Read PDF
d: Document = PDF.read("input.pdf")

# Process the PDF to get the text
output = Pipeline(
    [
        Source(),
        ByFont("Helvetica"),
        GetText(),
    ]
).process(d)

# Print the text
print(output.get(0, ""))

```

### Filtering by font_color

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_11_04.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_11_04.ipynb
from borb.pdf import ByFontColor
from borb.pdf import Document
from borb.pdf import GetText
from borb.pdf import PDF
from borb.pdf import Pipeline
from borb.pdf import Source
from borb.pdf import X11Color

import pathlib
import requests


def download_pdf(url: str, filename: pathlib.Path) -> None:
    """
    Downloads a PDF file from the given URL and saves it to the specified filename.

    :param url: The URL of the PDF file to download.
    :param filename: The local file path where the downloaded PDF should be saved.

    :raises requests.exceptions.RequestException: If there is an issue with the HTTP request (e.g., network failure, invalid URL, bad response).
    :raises Exception: If any other unexpected error occurs during file writing.
    """
    try:
        response = requests.get(url, stream=True)
        response.raise_for_status()  # Raise an exception for bad status codes
        with open(filename, "wb") as pdf_file:
            for chunk in response.iter_content(chunk_size=8192):
                pdf_file.write(chunk)
    except requests.exceptions.RequestException as e:
        print(f"Error downloading PDF: {e}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")


# Download
download_pdf(
    "https://raw.githubusercontent.com/jorisschellekens/borb-pdf-corpus/master/0001.pdf",
    pathlib.Path("input.pdf"),
)

# Read PDF
d: Document = PDF.read("input.pdf")

# Process the PDF to get the text
output = Pipeline(
    [
        Source(),
        ByFontColor(font_color=X11Color.RED),
        GetText(),
    ]
).process(d)

# Print the text
print(output)
```

### Filtering by font_size

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_11_05.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_11_05.ipynb
# TODO
```

## Applying a regular expression to a PDF

Maybe you’re looking for something very specific—like a phone number, an email address, or an invoice ID. Instead of extracting all text and manually searching through it, you can apply a regular expression to find exactly what you need.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_11_06.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_11_06.ipynb
# TODO
```

## Getting keywords from a PDF

If you need to quickly summarize what a document is about, keyword extraction can be incredibly helpful. Instead of reading through an entire PDF, you can extract key terms that provide a snapshot of its content.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_11_07.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_11_07.ipynb
from borb.pdf import Document
from borb.pdf import GetKeywordsByPagewiseTFIDF
from borb.pdf import GetText
from borb.pdf import PDF
from borb.pdf import Pipeline
from borb.pdf import Source

import pathlib
import requests


def download_pdf(url: str, filename: pathlib.Path) -> None:
    """
    Downloads a PDF file from the given URL and saves it to the specified filename.

    :param url: The URL of the PDF file to download.
    :param filename: The local file path where the downloaded PDF should be saved.

    :raises requests.exceptions.RequestException: If there is an issue with the HTTP request (e.g., network failure, invalid URL, bad response).
    :raises Exception: If any other unexpected error occurs during file writing.
    """
    try:
        response = requests.get(url, stream=True)
        response.raise_for_status()  # Raise an exception for bad status codes
        with open(filename, "wb") as pdf_file:
            for chunk in response.iter_content(chunk_size=8192):
                pdf_file.write(chunk)
    except requests.exceptions.RequestException as e:
        print(f"Error downloading PDF: {e}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")


# Download
download_pdf(
    "https://raw.githubusercontent.com/jorisschellekens/borb-pdf-corpus/master/0080.pdf",
    pathlib.Path("input.pdf"),
)

# Read PDF
d: Document = PDF.read("input.pdf")

# Process the PDF to get the keywords
output = Pipeline(
    [
        Source(),
        GetKeywordsByPagewiseTFIDF(),
    ]
).process(d)

# Print the keywords
print(output)


```

This prints the following:

```commandline
{
   "stairway":4.852030263919617,
   "fireresisting":4.1588830833596715,
   "window":4.1588830833596715,
   "separate":2.772588722239781,
   "escape":2.772588722239781,
   "houses":2.772588722239781,
   "resistance":2.772588722239781,
   "doors":2.0794415416798357,
   "rooflight":2.0794415416798357,
   "converting":2.0794415416798357
}

```

If you’re processing hundreds of PDFs, this technique allows you to automatically tag and categorize documents based on their content.

## Getting images from an existing PDF

If your PDF contains images—like scanned documents, charts, or figures—you may want to extract them for further analysis or storage.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_11_08.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_11_08.ipynb
from borb.pdf import Document
from borb.pdf import GetImages
from borb.pdf import PDF
from borb.pdf import Pipeline
from borb.pdf import Source

import pathlib
import requests


def download_pdf(url: str, filename: pathlib.Path) -> None:
    """
    Downloads a PDF file from the given URL and saves it to the specified filename.

    :param url: The URL of the PDF file to download.
    :param filename: The local file path where the downloaded PDF should be saved.

    :raises requests.exceptions.RequestException: If there is an issue with the HTTP request (e.g., network failure, invalid URL, bad response).
    :raises Exception: If any other unexpected error occurs during file writing.
    """
    try:
        response = requests.get(url, stream=True)
        response.raise_for_status()  # Raise an exception for bad status codes
        with open(filename, "wb") as pdf_file:
            for chunk in response.iter_content(chunk_size=8192):
                pdf_file.write(chunk)
    except requests.exceptions.RequestException as e:
        print(f"Error downloading PDF: {e}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")


# Download
download_pdf(
    "https://raw.githubusercontent.com/jorisschellekens/borb-pdf-corpus/master/0001.pdf",
    pathlib.Path("input.pdf"),
)

# Read PDF
d: Document = PDF.read("input.pdf")

# Process the PDF to get the text
output = Pipeline(
    [
        Source(),
        GetImages()
    ]
).process(d)

# Print the text
print(output)
```

## Debugging a PDF

Ever wondered what’s really inside a PDF? Think of a PDF as a structured document similar to JSON—it has primitive types (str, int, float, bool), collection types (dict, list), and references. borb lets you visualize this structure, making it easier to debug and understand PDFs at a low level.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_11_09.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_11_09.ipynb
from borb.pdf import Document
from borb.pdf import GetDocumentAsGraphML
from borb.pdf import Image
from borb.pdf import Lipsum
from borb.pdf import Page
from borb.pdf import PageLayout
from borb.pdf import Paragraph
from borb.pdf import PDF
from borb.pdf import Pipeline
from borb.pdf import SingleColumnLayout
from borb.pdf import Source
from borb.pdf import X11Color

# step 1: build PDF
d: Document = Document()
p: Page = Page()
d.append_page(p)
l: PageLayout = SingleColumnLayout(p)
l.append_layout_element(
    Paragraph(
        Lipsum.generate_lorem_ipsum(32),
        font_size=20,
        font_color=X11Color.PRUSSIAN_BLUE,
    )
)
l.append_layout_element(
    Image(
        bytes_path_pil_image_or_url="https://images.unsplash.com/photo-1732130318657-c8740c0f5215",
        size=(128, 128),
    )
)
l.append_layout_element(Paragraph(Lipsum.generate_lorem_ipsum(512)))
PDF.write(what=d, where_to="assets/output.pdf")

# step 2: read PDF
d: Document = PDF.read("assets/output.pdf")

# step 3: process
Pipeline([Source(), GetDocumentAsGraphML(where_to="assets/output.graphml")]).process(d)


```

Afterwards we can use a tool like yEd to visualize the resulting graphML file:

## Merging two (or more) PDFs

Need to combine multiple PDFs? Whether you’re assembling reports or collating research papers, merging PDFs is a common task.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_11_10.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_11_10.ipynb
# TODO
```

### Adding and Removing Pages to a PDF

What if you want to add pages to an existing PDF? Or remove unnecessary pages? borb makes it easy to manipulate PDF structures programmatically.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_11_11.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_11_11.ipynb
# TODO
```

```python3
```
