# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a collection of generative grammar oracles for the [PrestoPlot tool](https://github.com/eykd/prestoplot/), used for generating creative writing prompts, plot structures, and story elements. The oracles are written in YAML format and use PrestoPlot's grammar system for text generation.

## Repository Structure

- `oracles/` - Contains all oracle YAML files organized by category:
  - **Plot structures**: `lesterdentplot.yaml`, `generic-plot.yaml`, `propp.yaml`
  - **Characters**: `characters.yaml`, `villains.yaml`, `physical-description.yaml`
  - **Names**: Various name generators including Markov chain-based generators
  - **Genre-specific**: `noir.yaml`, `cyberpunk.yaml`, `western.yaml`, `spaceopera.yaml`, etc.
  - **Story elements**: `hooks.yaml`, `crises.yaml`, `outcomes.yaml`, `locations.yaml`


## Common Development Commands

To test oracles, use the `presto` command from the PrestoPlot tool:

```bash
# Generate from a specific oracle
presto run oracles/names-male-surname-markov.yaml

# Generate multiple outputs
presto run oracles/lesterdentplot.yaml -n 5
```

### YAML-based Oracle Grammar File Format
Oracle files use PrestoPlot's YAML grammar format with these key patterns:

- YAML-based with Python f-string syntax (default) OR jinja (by specifying `render: jinja` in the top-level namespace of an oracle file)
- Required `Begin:` stanza as entry point
- Support for `include:` to compose grammar files
- Modes: `reuse` (default), `pick` (no replacement), `markov` (chain generation)
- Seeded generation using keys (e.g., `{Name.character1}`)

### Basic Structure
- `Begin:` - Entry point for generation
- `include:` - References to other oracle files to import their rules
- Named rule sections defining generation patterns
- Text interpolation with conditional logic for pronouns (`{he.Protag}`, `{his.Antag}`)

### Grammar Syntax
- `{RuleName}` - References another top-level rule in the same or included file
- `{RuleName.key}` - Accesses specific variants or sub-rules
- `{RuleName[key]}` - Dynamic rule selection using context variables
  - For instance, a common pattern is to have different sub-rules based on sex:
    ```
    Name:
      - >
        {Names[Sex[key]][key]}

    Names:
      - male:
        - Bob
        - Frank
      - female:
        - Susie
        - Julie

    Sex:
      - male
      - female

    he:
      - >
        {'she' if Sex[key] == 'female' else 'he'}

    ```

    Here, `{Name.Protag}` may select either a male or a female name, as the
    special context variable `key` contains `"Protag"`.  `{he.Protag}` would be
    guaranteed to match the pronoun's gender to the name, and `{Sex.Protag}`
    would also reliably match.

### Key Patterns
1. **Setup files** (`setup.yaml`) - Define character archetypes and story foundations
2. **Markov generators** (`*-markov.yaml`) - Use statistical models for name generation
3. **Plot frameworks** - Structured story templates (Lester Dent, Propp functions)
4. **Modular components** - Reusable elements that can be mixed across genres

## Working with Oracle Files

### Adding New Oracles
1. Follow existing naming conventions (`genre.yaml`, `element-type.yaml`)
2. Use `include:` to leverage existing components rather than duplicating
3. Test generation with `presto run` before committing

### Modifying Existing Oracles
1. Understand the rule dependencies by checking which files include the oracle
2. Test changes don't break dependent oracles
3. Maintain consistent voice and style within genre categories

### Example Files to Understand
- `setup.yaml` - Core character and story setup templates used by many other oracles
- `characters.yaml` - Character archetypes and traits
- `lesterdentplot.yaml` - Classic pulp fiction plot structure template

### Key Utility files

- `sex.yaml` - Sex and gender-related utilities, especially pronouns
  - Provides `Sex`, `boy`, `man`, and various sex-based pronoun selectors w/
    lowercase and title capitalizations, e.g. `he` / `He` to produce `he`/`she`
    or `He`/`She` depending on the result of `Sex[key]`.

- `names.yaml` - Large collection of typical American names (very large file)
  - Provides `Names.male` and `Names.female` namespaces.

- `names-female.yaml` / `names-male.yaml` - the contents of `Names.male` and
  `Names.female` in `FemaleNames` and `MaleNames` namespaces.

- `names-markov.yaml` - Large Markov chain data for fantasy/sci-fi name generation (very large file)
  - Provides `NamesMarkov.male` and `NamesMarkov.female` namespaces.

- Likewise, `names-female-markov.yaml` and `names-male-markov.yaml` provide `

## Content Sources and Attribution

Oracle content derives from multiple sources including Abulafia generators, TVTropes, the Lester Dent Master Plot Formula, and Plotto. All content is distributed under Creative Commons Attribution-NonCommercial-ShareAlike 4.0 license.
