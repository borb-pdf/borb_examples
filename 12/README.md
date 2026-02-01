# 12. About the add-ons

![enter image description here](img/undraw_fill_the_blank.png)

This chapter introduces the `borb` add-ons: a set of optional, production-grade extensions that build on top of the core borb PDF engine. Each add-on is designed to solve a focused, real-world problem that commonly arises once PDF generation or processing moves beyond basic layouts. They integrate seamlessly with the existing `borb` API and follow the same design philosophy: composable, explicit, and predictable.

Add-ons are distributed separately from the open-source core library. This keeps `borb` lightweight while allowing advanced functionality to evolve independently and sustainably. If you only need the core features, nothing changes. If you need more power—text transformation, OCR, redaction, or higher-level document authoring—you can selectively enable exactly what you need.

The available add-ons covered in this chapter are:

- `borb_find_replace` for robust text search and replacement in existing PDFs
- `borb_markdown_to_pdf` for converting Markdown documents into high-quality PDFs
- `borb_ocr` for extracting text from scanned or image-based documents
- `borb_redact` for permanently removing sensitive content from PDFs

Each section in this chapter explains the purpose of the add-on, its installation, and practical usage examples. The goal is to help you quickly evaluate whether an add-on fits your workflow and to integrate it with confidence when it does.

## `borb_find_replace`

The `borb_find_replace` add-on provides a straightforward way to locate and replace text in existing PDF documents. It is intentionally designed for simple, deterministic find-and-replace operations where the structure and layout of the document must remain unchanged. This makes it well suited for tasks such as correcting typos, updating identifiers, or swapping fixed phrases across a document.

Unlike a word processor, `borb_find_replace` does not reflow text or recompute layout. Replacements occur in place, within the spatial constraints of the original content. As a result, it works best when the replacement text is similar in length and formatting to what it replaces.

Internally, the add-on removes the matched content and renders the replacement text into the newly freed space. This approach preserves surrounding layout and avoids unintended side effects, while making the limitations of the operation explicit and predictable.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_12_01.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_12_01.ipynb
# TODO
```

## `borb_markdown_to_pdf`

The `borb_markdown_to_pdf` add-on turns Markdown into fully laid-out PDF documents using borb’s layout engine. It is intended for cases where Markdown is used as a concise, human-friendly authoring format, but the output needs to meet the visual and structural expectations of a professionally generated PDF.

Rather than treating Markdown as a one-off conversion step, this add-on maps Markdown elements directly to borb layout primitives. Headings, lists, code blocks, tables, and inline formatting are translated into explicit layout constructs, giving you predictable results and fine-grained control over typography, spacing, and page structure.

Because the output is generated through `borb` itself, Markdown documents can be styled, extended, and combined with programmatically generated content. This makes `borb_markdown_to_pdf` a good fit for documentation, reports, and pipelines where Markdown is the source of truth but PDF is the final, distributable artifact.

The following example demonstrates the most basic usage of `borb_markdown_to_pdf`. It shows how a simple Markdown document can be converted into a PDF with minimal setup, focusing on the core idea: Markdown in, well-structured PDF out. This example is intentionally small, making it easy to understand how the add-on fits into an existing `borb` workflow.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_12_02.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_12_02.ipynb
from borb_markdown_to_pdf.pdf.visitor.markdown import Markdown
from borb.pdf import PDF

# define input
markdown_input: str = ""
markdown_input += "# Smoke Test\n"
markdown_input += "\n"
markdown_input += "This is a **minimal** markdown document.\n"
markdown_input += "\n"
markdown_input += "- One list item\n"
markdown_input += "- Another list item\n"
markdown_input += "\n"
markdown_input += "Inline code: `x = 1`\n"
markdown_input += "\n"
markdown_input += "> Blockquote\n"
markdown_input += "\n"
markdown_input += "---\n"
markdown_input += "\n"
markdown_input += "End.\n"
markdown_input += "\n"

# output
PDF.write(
  what=Markdown.to_pdf(markdown_input),
  where_to="assets/output.pdf"
)
```

That said, `borb_markdown_to_pdf` is not limited to trivial inputs. It also supports more complex Markdown constructs—such as nested lists, code blocks, tables, and richer inline formatting—while preserving structure and layout. The next example builds on the same principles, but demonstrates how the add-on handles more involved Markdown documents without sacrificing clarity or control.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_12_03.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_12_03.ipynb
from borb_markdown_to_pdf.pdf.visitor.markdown import Markdown
from borb.pdf import PDF

# define input
markdown_input: str = ""
markdown_input += "# Extended Smoke Test\n"
markdown_input += "\n"
markdown_input += "This document exercises **more complex** Markdown features:\n"
markdown_input += "\n"
markdown_input += "## Features Overview\n"
markdown_input += "\n"
markdown_input += "- Nested lists\n"
markdown_input += "  - Bullet level 2\n"
markdown_input += "    - Bullet level 3\n"
markdown_input += "- Inline formatting like *italics*, **bold**, and `code`\n"
markdown_input += "- Tables\n"
markdown_input += "- Fenced code blocks\n"
markdown_input += "\n"
markdown_input += "## Data Table\n"
markdown_input += "\n"
markdown_input += "| Column A | Column B | Column C |\n"
markdown_input += "|----------|----------|----------|\n"
markdown_input += "| Alpha    | 123      | True     |\n"
markdown_input += "| Beta     | 456      | False    |\n"
markdown_input += "| Gamma    | 789      | True     |\n"
markdown_input += "\n"
markdown_input += "## Code Example\n"
markdown_input += "\n"
markdown_input += "```python\n"
markdown_input += "def add(a, b):\n"
markdown_input += "    return a + b\n"
markdown_input += "\n"
markdown_input += "result = add(2, 3)\n"
markdown_input += "print(result)\n"
markdown_input += "```\n"
markdown_input += "\n"
markdown_input += "## Notes\n"
markdown_input += "\n"
markdown_input += "> This blockquote spans multiple lines and is meant to test\n"
markdown_input += "> layout behavior in more realistic documents.\n"
markdown_input += "\n"
markdown_input += "---\n"
markdown_input += "\n"
markdown_input += "For more information, visit [borb on GitHub](https://github.com/jorisschellekens/borb).\n"
markdown_input += "\n"
markdown_input += "End of extended test.\n"
markdown_input += "\n"

# output
PDF.write(
    what=Markdown.to_pdf(markdown_input),
    where_to="output.pdf"
)

```

## `borb_ocr`

This example demonstrates the most basic end-to-end usage of `borb_ocr`. It starts by creating a small image containing text, embeds that image into a PDF, and then reads the document back for processing. This keeps the setup fully self-contained and avoids any external files or scanners.

The document is then passed through a simple Pipeline that applies OCR and extracts the recognized text. By printing the result, the example makes it easy to verify that the text embedded in the image is successfully detected and recovered, illustrating the core purpose of the add-on with minimal moving parts.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_12_04.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_12_04.ipynb
from borb.pdf import Document
from borb.pdf import Page
from borb.pdf import SingleColumnLayout
from borb.pdf import Paragraph

# create a (PIL) image that has the text 'Hello World' in it
from PIL import Image, ImageDraw, ImageFont  # type: ignore[import-not-found]

img = Image.new("RGB", (100, 100), color="white")
draw = ImageDraw.Draw(img)
draw.text((0, 0), "Hello World", fill="black", font=ImageFont.load_default())

# create empty Document
doc = Document()

# create empty Page
page = Page()
doc.append_page(page)

# create PageLayout
layout = SingleColumnLayout(page)

# add img
from borb.pdf import Image as bImage  # type: ignore[import-not-found]
layout.append_layout_element(bImage(img))

# write
PDF.write(what=doc, where_to="assets/test_smoke.pdf")

# read
doc = PDF.read(where_from="assets/test_smoke.pdf")

# process
txt = Pipeline([Source(), ApplyOCR(), GetText()]).process(doc)

# print
print(txt[0])

```

## `borb_redact`

The following example illustrates the most direct way to use `borb_redact`. It starts by creating a simple PDF document, then manually adds a redact annotation by explicitly specifying its coordinates. This makes the mechanics of redaction clear and keeps the example focused on the core concepts rather than automation or discovery.

Once the redact annotation is in place, a `Pipeline` is used to apply the redactions and produce the final document. This step is critical: redact annotations describe what should be removed, while the pipeline is responsible for permanently removing the underlying content. More advanced workflows—such as locating content automatically—build on this same foundation.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_12_05.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_12_05.ipynb
from borb.pdf import Document
from borb.pdf import Page
from borb.pdf import SingleColumnLayout
from borb.pdf import Paragraph

# create empty Document
doc = Document()

# create empty Page
page = Page()
doc.append_page(page)

# create PageLayout
layout = SingleColumnLayout(page)

# add random text
random.seed(0)
for _ in range(5):
  layout.append_layout_element(
    Paragraph(
      text=Lipsum.generate_lorem_ipsum(512),
      text_alignment=LayoutElement.TextAlignment.JUSTIFIED,
    )
  )

# add annotation
w, h = page.get_size()
RedactionAnnotation().paint(available_space=(0, h - 100, 100, 100), page=page)

# process
Pipeline(
  [
    Source(),
    ApplyRedactionAnnotations(remove_applied_redact_annotations=True),
  ]
).process(doc)

PDF.write(what=doc, where_to="output.pdf")

```

In the next example, redaction is driven by content rather than coordinates. Instead of manually specifying regions, a regular expression is used to locate text that should be removed. This approach is useful when sensitive information follows recognizable patterns, such as identifiers, account numbers, or repeated phrases.

The add-on translates these matches into redact annotations automatically, after which the same pipeline-based process is applied to permanently remove the content. This demonstrates how borb_redact can scale from precise, manual control to rule-based redaction while remaining explicit and auditable.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_12_06.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_12_06.ipynb
# TODO
```

The final example builds on the same idea, but replaces custom regular expressions with the built-in pattern classes provided by `borb_redact`. These classes encapsulate commonly redacted data types—such as IP addresses, IBANs, personal names, and similar structured identifiers—so you can apply well-tested patterns without writing or maintaining your own expressions.

Using these predefined matchers makes redaction rules more readable, more consistent, and less error-prone, especially in larger pipelines. The underlying workflow remains unchanged: matches are converted into redact annotations and then applied through a pipeline to permanently remove the sensitive content.

<a href="https://colab.research.google.com/github/jorisschellekens/borb-examples/blob/master/11/ipynb/snippet_12_07.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

```python3
# snippet_12_07.ipynb
# TODO
```
