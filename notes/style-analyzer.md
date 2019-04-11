# Style-Analyzer: Fixing Code Style Inconsistencies with Interpretable Unsupervised Algorithms (2019)

Vadim Markovtsev, Waren Long, Hugo Mougard, Konstantin Slavnov, Egor Bulychev

---

Source code reviews take time, but having consistent formatting withing coding projects reduces the time this takes and also allows developers to spot bugs easier during code reviews. Linters exist, but these are difficult to configure and only highlight erroneous formatting.

They introduce Style-Analyzer (SA) which automatically formats code to be consistent with the rest of the project it is part of. SA uses a machine learning model to learn code formatting rules from existing code and can learn online from developer feedback, e.g. can disable certain formatting rules. The style is learned through a language model and a decision tree forest on the code's abstract syntax tree (AST).

There's also a whole pipeline surrounding this work that watches GitHub repositories and suggests changes on pull requests, along with a web application with annotation/visualization of the rules extracted.

Previous works are Naturalize and CodeBuff. Naturalize generates formatting candidates and then ranks them, but only considers local context and sometimes messes up semantics. CodeBuff uses machine learning to extract rules but needs to be configured on every example and does not cover tabs vs. spaces or single vs. double quotations.

SA learns completely unsupervised from a language model of the source code. The language model predicts whether new, proposed code changes follow the established conventions.

They represent code as a linear sequence of tokens. Tokens represent either leaves in the AST or whitespace characters. They use their Babelfish tool that extracts universal ASTs (UASTs). ASTs of different languages are very different, e.g. different internal nodes, but UASTs of different languages are similar. 

The formatting elements they care about are:

- whitespace
- tabs
- newline
- whitespace indentation
- whitespace dedentation
- tab indentation
- tab dedentation
- single quote
- double quote
- empty gap between tokens where at least one is a non-leaf node in the AST

They also make classes out of a combination of these elements, e.g. four spaces, double newlines, etc.

They use sequence prediction to model insertions and deletions, i.e. the output of their model is a sequence. To extract features from the code they use a fixed window around the current target (both behind and in front) as well as a few levels of the AST around this point. They use 5 tokens to the left and right, and 2 AST parents upwards.

Their UAST has the following features:

- value: the content of the corresponding token, empty string if does not exist
- internal type: string which indicates the type of the node in the native AST
- role: similar to above but from the UAST, around 200 different roles in their Babelfish UAST
- start position: line and column number where node begins
- end position: line and column where node ends

For each formatting element in their model, the features are:

- label: the one-hot encoded label of this formatting token, only for nodes left of label
- internal type: one-hot encoded, all zeros if no corresponding UAST node
- reserved index: for those with no internal type it is all possible token values in the analyzed file(?)
- length: length of token
- roles: the roles from the UAST node
- position information: only for nodes left of label, zeros for those on right
- file, column and line positions

The reason they treat nodes to the right differently is to avoid information leakage.

They use decision trees as they are interpretable (white box) models. Specifically they use an ensemble of these to construct a decision forest, which is optimized via Baysian optimization.

When using in production, they raise a suggestion for every single prediction that does not match the label. If there are multiple suggestions per label, they use the most confident one. If any of their suggestions cause the AST to change then the suggestion is dropped. This is how they avoid changing the semantics or causing errors.

They train on the 19 most starred JavaScript projects, using 80% of the files for the training set.