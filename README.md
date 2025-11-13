Knowledge Graph Construction & Network Analysis of EU Parliamentary Debates
Project Overview

This project builds a knowledge graph and network analysis pipeline for speeches from the European Parliament, using modern NLP techniques and Large Language Models (LLMs). The goal is to extract structured information from political speeches, group them into meaningful thematic clusters, and analyze the relationships between parties, speakers, and topics using graph-based methods.

The final output is a set of interactive and analytical visualizations that reveal the hidden structure of political discourse within the European Parliament.

📦 Dataset Used
Source Dataset

We use the publicly available dataset:

RJuro/eu_debates (HuggingFace)


This dataset contains:

Speech transcripts

Speaker names

Speaker political party

Language

Date

Metadata (debate titles, document IDs)

Machine-translated English version of each speech

Rationale for Subset Selection

The full dataset spans multiple years and languages, with thousands of speeches. To ensure:

manageable computational cost

consistent political context

clean network structure

We restricted the dataset to:

All speeches delivered in 2023
→ narrowed to a random sample of ~1000 speeches for LLM extraction.

This preserves:

temporal coherence

thematic consistency

political relevance (same parliamentary composition)
while being computationally practical when running LLM calls for extraction and clustering.

🤖 LLM Extraction Pipeline
1. Preprocessing

We created a final_text column using:

translated_text when available

otherwise original text

Empty, “null”, and malformed entries were removed.

2. Topic Extraction Using Gemini

We prompted Google’s Gemini-Pro API to extract:

1–3 high-level topics from each speech

a primary topic summarizing the main theme

output in strict JSON format

Additional measures:

JSON cleaning and recovery

retry logic for malformed responses

text truncation for very long speeches

This produced a standardized primary_topic for each speech.

3. Topic Cluster Generation

Rather than manually defining clusters, we instructed Gemini to:

“Cluster this list of primary topics into a small number of coherent, high-level thematic groups.”

Gemini generated:

The number of clusters

A label and description for each cluster

Membership lists

Topics were mapped back into topic_cluster_label, representing one of the LLM-defined issue clusters.

🕸️ Knowledge Graph Construction

We constructed three main graphs from docs_df, each representing a different layer of the parliamentary debate structure.

1. Speaker → Topic Cluster Graph

Bipartite graph linking:

Speakers

Topic clusters

Edges = speaker discussed that cluster
Weights = number of speeches

Used for:

Identifying issue generalists

Understanding which themes attract broad vs. narrow engagement

2. Party → Speaker → Topic Cluster (3-Mode Graph)

A heterogeneous, tripartite knowledge graph:

Party  →  Speaker  →  Topic Cluster


Used for:

Exploring party-level issue portfolios

Visualizing the flow of political attention

Identifying cross-party consensus and divergence

Understanding how parties allocate thematic attention across speakers

This graph was visualized interactively using PyVis, enabling zoom, hover, and dynamic exploration.

3. Speaker → Speaker Network

One-mode projection where two speakers are connected if they share at least one topic cluster.
Edge weight = number of shared clusters.

Used for:

Similarity analysis

Identifying core debate actors

Detecting thematic communities of speakers

📊 Centrality Analysis

We computed centrality metrics on the key graphs:

✔ Degree Centrality

Measures the breadth of connectivity.
Identifies:

“generalist” speakers

widely discussed clusters

parties engaging in many thematic areas

✔ Weighted Degree (Strength)

Measures total volume of speech activity.
Highlights:

heavily debated themes

the most active speakers/parties

✔ Betweenness Centrality

Identifies bridges connecting otherwise separate parts of the network.
Shows:

cross-cutting speakers

integrating themes

parties linking different ideological spaces

✔ Closeness Centrality

Identifies globally central nodes.
Shows:

who is proximal to the entire debate structure

which topics are central across all discussions

🏆 Summary of Key Findings
1. Governance, Economy, and Foreign Policy dominate debates

Across degree, strength, betweenness, and closeness:

EU Governance & Rule of Law

Economy & Competitiveness

Foreign Policy & Defence

are the most central thematic hubs.

2. A few speakers play outsized roles

Ladislav Ilčić is the most central MEP by all measures:

most connected (degree)

most active (strength)

biggest bridge (betweenness)

most globally central (closeness)

Other influential speakers:

Roberta Metsola

Janusz Wojciechowski

Gunnar Beck

Jaak Madison

3. Some parties have broader thematic portfolios

S&D is the most central party across all metrics.

N/A (non-attached) surprisingly high in speech volume.

PPE and ECR occupy central positions linking multiple clusters.

Smaller parties (Greens/EFA, GUE/NGL, ID) are more specialized.

4. Cross-party bridges exist

Certain topics (e.g. Governance, Human Rights) create cross-ideological touchpoints.

⚠️ Limitations
1. LLM variability & lack of determinism

LLM outputs vary slightly with different prompts; cluster definitions are not guaranteed reproducible.

2. Potential for semantic drift

LLM-inferred topics may group semantically distinct but lexically similar concepts.

3. Bias & hallucination risk

Despite constraints, LLMs occasionally produce invalid JSON or drifting topic labels.

4. Temporal limitation

The analysis is restricted to 2023; dynamics across years remain unexplored.

5. Subset effects

Using a sample of 1000 speeches reduces computational load but may miss rare thematic patterns.

🚀 Future Directions

Extend analysis to multiple years to detect evolution of issue salience

Apply community detection (Louvain) to identify political “clusters” of speakers

Add sentiment or stance dimensions to edges

Integrate formal roles (committee membership, leadership positions)

Use embeddings instead of LLM clustering for stability and reproducibility

📌 Final Remark

This project demonstrates how LLM-based extraction + graph analysis can transform unstructured political speech into a structured, relational model of discourse. The resulting knowledge graph offers a powerful, explainable view of how themes, speakers, and parties interact in shaping parliamentary debate.
