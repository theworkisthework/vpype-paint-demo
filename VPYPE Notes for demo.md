VPYPE Notes for demo - Requires Python 3.10 - 3.12

Setup venv:

`python -m venv .venv`

Activate venv:

`.venv\Scripts\activate`

Install vpype: (might need to `pip install pipx` )

`pipx install "vpype[all]"`

Test vpype works:

`vpype --version` or `vpype --help`

Next we need a machine profile:

See `vpype-gcode.toml`

See profiles available in that file. Solenoid (M3 S0/M3 S1) or Servo (G0 Z-12/G0 Z12)

Run command to generate gcode from svg (Use the appropriate profile)

`vpype --config vpype-gcode.toml read input.svg gwrite --profile terrapen-bottom-left output.gcode`

Painting with vpype

We take the original SVG along with several 'dipping pattern' SVGs and combine them together. We use vpype to split
the original SVG by line length and insert a dipping pattern for every `splitdist` (1m in the example). The dipping
pattern SVGs determine where the paint should be. Each layer in the SVG corresponds to a different paint colour and
a differnt dipping pattern location.

See docs: `https://vpype.readthedocs.io/en/stable/cookbook.html#splitting-layers-by-drawing-distance`

Doc example that uses layer name does not work:

`vpype read ai-starry-night*plotted_2_pens1_to_4.svg forlayer lmove %\_lid% 1 splitdist 1m forlayer lmove %\_lid% "%\_lid*2%" read -l "%\_lid*2-1%" ./dip/dip*%\_name%.svg end lmove all %\_lid% name -l %\_lid% %\_name% color -l %\_lid% %\_color% end write dip_output.svg`

Swap layer name for layer id `lid` and it works:

`vpype read ai-starry-night_plotted_2_pens1_to_4.svg forlayer lmove %_lid% 1 splitdist 1m forlayer lmove %_lid% "%_lid*2%" read -l "%_lid*2-1%" dip_%_lid%.svg end lmove all %_lid% name -l %_lid% %_name% color -l ./dip/%_lid% %_color% end write dip_output.svg`

View file to see added dipping patterns:

`vpype read dip_output.svg show`

Translate SVG:

`vpype read ai-starry-night_plotted_2_pens1_to_4.svg translate 1cm 80cm forlayer lmove %_lid% 1 splitdist 1m forlayer lmove %_lid% "%_lid*2%" read -l "%_lid*2-1%" ./dip/dip_%_lid%.svg end lmove all %_lid% name -l %_lid% %_name% color -l %_lid% %_color% end write dip_output.svg`

Convert it to gcode:

`vpype --config vpype-gcode.toml read dip_output.svg gwrite --profile terrapen-bottom-left dip_output.gcode`

Or use Lightburn
