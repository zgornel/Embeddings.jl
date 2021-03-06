# Embeddings

[![Stable](https://img.shields.io/badge/docs-stable-blue.svg)](https://JuliaText.github.io/Embeddings.jl/stable)
[![Latest](https://img.shields.io/badge/docs-latest-blue.svg)](https://JuliaText.github.io/Embeddings.jl/latest)
[![Build Status](https://travis-ci.org/JuliaText/Embeddings.jl.svg?branch=master)](https://travis-ci.org/JuliaText/Embeddings.jl)
[![Build Status](https://ci.appveyor.com/api/projects/status/github/JuliaText/Embeddings.jl?svg=true)](https://ci.appveyor.com/project/JuliaText/Embeddings-jl)
[![CodeCov](https://codecov.io/gh/JuliaText/Embeddings.jl/branch/master/graph/badge.svg)](https://codecov.io/gh/JuliaText/Embeddings.jl)
[![Coveralls](https://coveralls.io/repos/github/JuliaText/Embeddings.jl/badge.svg?branch=master)](https://coveralls.io/github/JuliaText/Embeddings.jl?branch=master)


## Introduction

Word Embeddings are great.
Basically the foundation of a huge pile of NLP work in the last decade and a half.
Use them directly, or use them to initalize a neural network embedding layer.
This package exposes access to pretrained embeddings.

At present it offers the famous [Word2Vec](https://code.google.com/archive/p/word2vec/) embeddings for English only, [GloVe](https://nlp.stanford.edu/projects/glove/) embeddings for English only, and [FastText](https://fasttext.cc/) embeddings for hundreds of languages.


## Example

Load the package with

```
julia> using Embeddings
```


load up the default word2vec embeddings:
```
julia> load_embeddings(Word2Vec) 
Embeddings.EmbeddingTable{Array{Float32,2},Array{String,1}}(Float32[0.0673199 0.0529562 … -0.21143 0.0136373; -0.0534466 0.0654598 … -0.0087888 -0.0742876; … ; -0.00733469 0.0108946 … -0.00405157 0.0156112; -0.00514565 -0.0470722 … -0.0341579 0.0396559], String["</s>", "in", "for", "that", "is", "on", "##", "The", "with", "said"  …  "#-###-PA-PARKS", "Lackmeyer", "PERVEZ", "KUNDI", "Budhadeb", "Nautsch", "Antuane", "tricorne", "VISIONPAD", "RAFFAELE"])
```


Load up the first 100 embeddings from the default French FastText embeddings:
```
julia> load_embeddings(FastText_Text{:fr}; max_vocab_size=100) 
Embeddings.EmbeddingTable{Array{Float32,2},Array{String,1}}(Float32[0.0058 -0.0842 … -0.062 -0.0687; 0.0478 -0.0388 … 0.0064 -0.339; … ; 0.023 -0.0106 … -0.022 -0.1581; 0.0378 0.0579 … 0.0417 0.0714], String[",", "de", ".", "</s>", "la", "et", ":", "à", "le", "\""  …  "faire", "c'", "aussi", ">", "leur", "%", "si", "entre", "qu", "€"])
```


List all the default files for FastText in English:
```
julia> language_files(FastText_Text{:en}) # List all the possible default files for FastText in English
3-element Array{String,1}:
 "FastText Common Crawl/crawl-300d-2M.vec"
 "FastText Wiki News/wiki-news-300d-1M.vec"
 "FastText en Wiki Text/wiki.en.vec"
```

From the second of those default files, load the embeddings just for "red", "green", and "blue": 
```
julia> load_embeddings(FastText_Text{:en}, 2; keep_words=Set(["red", "green", "blue"]))
Embeddings.EmbeddingTable{Array{Float32,2},Array{String,1}}(Float32[-0.0054 0.0404 -0.0293; 0.0406 0.0621 0.0224; … ; 0.218 0.1542 0.2256; 0.1315 0.1528 0.1051], String["red", "green", "blue"])
```

List all the default files for GloVe in English:
```
julia> language_files(GloVe{:en})
10-element Array{String,1}:
 "glove.6B/glove.6B.50d.txt"
 "glove.6B/glove.6B.100d.txt"
 "glove.6B/glove.6B.200d.txt"
 "glove.6B/glove.6B.300d.txt"
 "glove.42B.300d/glove.42B.300d.txt"
 "glove.840B.300d/glove.840B.300d.txt"
 "glove.twitter.27B/glove.twitter.27B.25d.txt"
 "glove.twitter.27B/glove.twitter.27B.50d.txt"
 "glove.twitter.27B/glove.twitter.27B.100d.txt"
 "glove.twitter.27B/glove.twitter.27B.200d.txt"
```

Load the 200d GloVe embedding matrix for the top 10000 words trained on 6B words:
```
julia> glove = load_embeddings(GloVe{:en}, 3, max_vocab_size=10000)
Embeddings.EmbeddingTable{Array{Float32,2},Array{String,1}}(Float32[-0.071549 0.17651 … 0.19765 -0.22419; 0.093459 0.29208 … -0.31357 0.039311; … ; 0.030591 -0.23189 … -0.72917 0.49645; 0.25577 -0.10814 … 0.07403 0.41581], ["the", ",", ".", "of", "to", "and", "in", "a", "\"", "'s"  …  "slashed", "23-year", "communique", "hawk", "necessity", "petty", "stretching", "taxpayer", "resistant", "quinn"])

julia> size(glove)
(200, 10000)
```

## Details


### `load_embeddings`

    load_embeddings(EmbeddingSystem, [embedding_file|default_file_number])
    load_embeddings(EmbeddingSystem{:lang}, [embedding_file|default_file_number])

Loaded the embeddings from a embedding file.
The embeddings should be of the type given by the Embedding system.

If the `embedding file` is not provided, a default embedding file will be used.
(It will be automatically installed if required).
EmbeddingSystems have a language type parameter.
For example `FastText_Text{:fr}` or `Word2Vec{:en}`, if that language parameter is not given it defaults to English.
(I am sorry for the poor state of the NLP field that many embedding formats are only available pretrained in English.)
Using this the correct  default embedding file will be installed for that language.
For some languages and embedding systems there are multiple possible files.
You can check the list of them using for example `language_files(FastText_Text{:de})`.
The first is nominally the most popular, but if you want to default to another you can do so by setting the `default_file_num`.

### This returns an `EmbeddingTable` object.
This has 2 fields.

 - `embeddings` is a matrix, each column is the embedding for a word.
 - `vocab` is a vector of strings, ordered as per the columns of `embeddings`, such that the first string in vocab is the first column of `embeddings` etc

We do not include a method for getting the index of a column from a word.
This is trivial to define in code (`vocab2ind(vocab)=Dict(word=>ii for (ii,word) in enumerate(vocab))`),
and you might like to be doing this in a more consistant way, e.g using [MLLabelUtils.jl](https://github.com/JuliaML/MLLabelUtils.jl),
or you might like to build a much faster Dict solution on top of [InternedStrings.jl](https://github.com/JuliaString/InternedStrings.jl)


## Configuration
This package is build on top of [DataDeps.jl](https://github.com/oxinabox/DataDeps.jl).
To configure, e.g., where downloaded files save to, and read from (and to understand how that works),
see the DataDeps.jl readme.


## Contributing

 - Provide Hashstrings: I have only filled in the checksums for the FastText Embeddings that I have downloaded, which is only a small fraction. If you using embeddings files for a language that doesn't have its hashstring set, then DataDeps.jl will tell you the hashstring that need to be added to the file. It is a quick and easy PR.
 - Provide Implementations of other loadered: If you have implementations of code to load another format (e.g. Binary FastTest) it would be great if you could contribute them. I know I have a few others kicking around somewhere.
