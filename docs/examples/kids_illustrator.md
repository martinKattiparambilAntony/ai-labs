# Kids Illustrator

The **Kids Illustrator** agent network turns a story idea into an illustrated comic page. It demonstrates a multi-agent creative workflow for children's content:

1. **Orchestrator** defines age-appropriate constraints, visual style, page limits, and character guides.
2. **Author** converts the concept into a panel-by-panel script.
3. **Art Director** turns each panel into a detailed image-generation prompt and tracks visual continuity.
4. **Illustrator** generates panel images with OpenAI Image Generation.
5. **Layout Editor** arranges panels and adds dialogue, captions, and sound effects.
6. **PDF Exporter** uses Code Interpreter to compose and save the final downloadable PDF.
7. **Front Man** coordinates the sequence and returns the completed file attachment.

## File

[kids_illustrator.hocon](../../registries/experimental/kids_illustrator.hocon)

## Requirements

- `OPENAI_API_KEY`
- OpenAI Responses API access for Image Generation and Code Interpreter
- The dependencies installed from `requirements.txt`
- Run commands from the repository root so the HOCON includes resolve correctly

The network uses the shared `openai_image_generation` and `openai_code_interpreter` toolbox definitions. Those shared definitions and their Python implementations are already part of this repository; they should be included when copying this network to another Neuro SAN Studio checkout.

## Run

```powershell
$env:OPENAI_API_KEY = "your-key"
python -m neuro_san_studio run --agent generated/kids_illustrator
```

Then try one of these prompts:

```text
Create a four-panel comic about a brave young inventor who fixes a moon rover.
Make a funny adventure comic for ages 8-10 about a hamster detective in a pet store.
Turn this story idea into a colorful comic page: two friends discover a talking tree at recess.
```

The successful response should contain a downloadable `kids_illustrator_comic.pdf` attachment. The PDF Exporter is explicitly instructed not to paste PDF bytes or base64 into chat. Generated image files are produced by the OpenAI image tool according to its configured file-saving behavior. The actual model response can vary, so the sample prompts are intended to demonstrate the workflow rather than guarantee identical artwork.

## Testing

The manual fixture at [kids_illustrator_test.hocon](../../tests/fixtures/generated/kids_illustrator_test.hocon) exercises the front man with a representative prompt. It is intentionally not part of the default integration suite because it invokes paid external OpenAI tools and requires an API key.

To run it through the dynamic HOCON test harness when credentials and tool access are available, add its path to `tests/integration/test_integration_test_hocons.py` or invoke it using the repository's dynamic HOCON test tooling.

## Architecture

```text
User
  -> orchestrator
       -> front_man
            -> author
            -> art_director
            -> illustrator -> openai_image_generation
            -> layout_editor
            -> pdf_exporter -> openai_code_interpreter
```

The network keeps story text and visual prompts separate from image generation, which makes each creative stage inspectable for review and easier to revise independently.
