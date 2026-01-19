### ContentUnderstanding:
These are the data structures I will be sending to ContentUnderstanding. They don't include all of the fields required by Azure, just the fields I care about (for now).
#### Analyzers:
| Field      | Type          |
| ---------- | ------------- |
| AnalyzerId | string        |
| Config     | string (json) |
| Fields     | string (json) |
| Models     | string (json) |
Analyzers contain the field definition.
#### Categorizers:
| Field      | Type                                       |
| ---------- | ------------------------------------------ |
| AnalyzerId | string                                     |
| Config     | string (json) - includes ContentCategories |
| Models     | string (json)                              |
Categorizers contain the ContentCategories. Setting up a Categorizer allows documents to be analyzed without defining an Analyzer. The ContentCategories section contains descriptions of which types of documents go to which Analyzer, passing the responsibility of determining the Analyzer to use to the Categorizer.

### Maestro:
This is the key right here. The way the data is stored in the workflow project is the main crux of the problem. 