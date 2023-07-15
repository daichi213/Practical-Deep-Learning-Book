# Code for Chapter 4: Building a Reverse Image Search Engine: Understanding Embeddings

Like Google Reverse Image Search, we explore how one can use embeddings — a contextual representation of an image to find similar images in under ten lines. And then the fun starts when we explore different strategies and algorithms to speed this up at scale, from thousands to several million images, and making them searchable in microseconds.

## Setup

Run the following command prior to running the scripts in this directory:

`$ pip install annoy -U`

## Code

Go through the code in the following order:

1. [1-feature-extraction.ipynb](https://github.com/practicaldl/Practical-Deep-Learning-Book/blob/master/code/chapter-4/1-feature-extraction.ipynb): We will extract features from pretrained models like VGG-16, VGG-19, ResNet-50, InceptionV3 and MobileNet and benchmark them using the Caltech101 dataset.
2. [2-similarity-search-level-1.ipynb](https://github.com/practicaldl/Practical-Deep-Learning-Book/blob/master/code/chapter-4/2-similarity-search-level-1.ipynb): We write an indexer to index features and search for most similar features using various nearest neighbor algorithms, and explore various methods of visualizing plots.
3. [2-similarity-search-level-2.ipynb](https://github.com/practicaldl/Practical-Deep-Learning-Book/blob/master/code/chapter-4/2-similarity-search-level-2.ipynb): we benchmark the algorithms based on the time it takes to index images and locate the most similar image based on its features using the Caltech-101 dataset. We also experiment with t-SNE and PCA.
4. [2-similarity-search-level-3.ipynb](https://github.com/practicaldl/Practical-Deep-Learning-Book/blob/master/code/chapter-4/2-similarity-search-level-3.ipynb): So far we experimented with different visualization techniques on the results, t-SNE and PCA on the results. Now we will calculate the accuracies of the features obtained from the pretrained and finetuned models. The finetuning here follows the same finetuning technique we learnt in Chapter 2.
5. [3-reduce-feature-length-with-pca.ipynb](https://github.com/practicaldl/Practical-Deep-Learning-Book/blob/master/code/chapter-4/3-reduce-feature-length-with-pca.ipynb): We will experiment with PCA and figure out what is the optimum length of the features to use in our experiments.
6. [4-improving-accuracy-with-fine-tuning.ipynb](https://github.com/practicaldl/Practical-Deep-Learning-Book/blob/master/code/chapter-4/4-improving-accuracy-with-fine-tuning.ipynb): Many of the pre-trained models were trained on the ImageNet dataset. Therefore, they provide an incredible starting point for similarity computations in most situations. That said, if you tune these models to adapt to your specific problem, they would perform even more accurately for finding similar images.

In this portion of the chapter, we will find the least accurate (worst) performing categories, visualize them with t-SNE, fine-tune and then see how their t-SNE graph changes.

## Data

We will be using the Caltech101 dataset. Please download the [Caltech101](http://www.vision.caltech.edu/Image_Datasets/Caltech101/101_ObjectCategories.tar.gz) dataset and place it in a `caltech101` directory.

# 尾崎学習

## embeddingについて

画像データなどの高次元特徴量を低次元空間へマッピングする手法のこと。**CNNを使用するとかなり精度よく高次元特徴量をマッピングすることが可能**。  
応用手法として、距離学習がある。

### 距離学習

実際に使用されている有名な事例は、顔認証システムで、登録時の本人の顔写真と認証時の顔写真をembedding⇒低次元空間でのユークリッド距離に応じて認証する・しないを判定する。  
職務経歴書と求職者の履歴書の類似度からマッチングを行うような提案もなされている。

## 類似検索

推薦システムなどで、入力値から類似しているデータを出力する検索などのこと。  
PoCやアカデミックレベルでは考慮する必要がないが、プロダクション環境で推薦システムなどを運用する場合、かなり高速で検索処理を実行する必要がある。
- レイテンシーによる損失について
   - Amazonが発表した調査によると、自社のWebサイトでレイテンシーが100ms増加するごとに利益が1%減少したとのこと
   - Googleの調査では、500msの遅延がトラフィックを20％減少させたとのこと

## [キリンと樹木問題](https://business101.com/an-ai-expert-explains-why-theres-always-a-giraffe-in-artificial-intelligence/#:~:text=An%20AI%20trained%20on%20examples,are%20no%20giraffes%20at%20all.)

- キリンの画像分類問題で、複雑なモデルを組むよりも、埋め込み表現のシンプルな最近傍法が結局分類精度が高くなる話
  - キリンの画像には樹木も一緒に映っている画像が多く、バイアスが入ってしまっているため、複雑なモデルを使用するとこのバイアスまで学習してしまう

## コールドスタート

インスタグラムのような画像ベースのsnsなどで、画像などに対してタグづけを行う場合、サービス上で新規の画像に対してはタグ付けを行うことができない（アルゴリズムベースの場合）。これをコールドスタートと呼んでいるよう。
