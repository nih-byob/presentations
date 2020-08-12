Example: Using Stanza Pre-trained Biomedical Models to Analyze Text from Pubmed
===============================================================================

**Overview**

In this example notebook, article text is retrieved from [Pubtator
API](https://www.ncbi.nlm.nih.gov/research/pubtator/api.html) and processed using the
[pre-trained biomedical models](https://stanfordnlp.github.io/stanza/biomed.html) from
the Stanza NLP framework.

For more information on the models, see:

- [[2007.14640] Biomedical and Clinical English Model Packages in the Stanza Python NLP Library (Zhang et al., 2020)](https://arxiv.org/abs/2007.14640)

**Setup**

In order to run this notebook, you will need to install the [Stanza NLP
package](https://stanfordnlp.github.io/stanza/index.html).

To create and activate a conda environment with all of the needed requirements, run:

```sh
conda create --file requirements.txt -c stanfordnlp -n stanza
conda activate stanza
```

Then, to launch the notebook, run:

```
jupyter notebook
```

**See Also**

- [Download & Usage - Stanza](https://stanfordnlp.github.io/stanza/biomed_model_usage.html)
- [Installation & Getting Started - Stanza](https://stanfordnlp.github.io/stanza/installation_usage.html)
- [PubTator Central API - NCBI - NLM - NIH](https://www.ncbi.nlm.nih.gov/research/pubtator/api.html)

