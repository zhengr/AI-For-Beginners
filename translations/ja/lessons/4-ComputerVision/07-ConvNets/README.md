# 畳み込みニューラルネットワーク

以前に述べたように、ニューラルネットワークは画像処理に非常に優れており、単層パーセプトロンでもMNISTデータセットから手書きの数字を比較的高い精度で認識することができます。しかし、MNISTデータセットは非常に特別で、すべての数字が画像の中央に配置されているため、タスクが簡単になります。

## [講義前クイズ](https://red-field-0a6ddfd03.1.azurestaticapps.net/quiz/107)

実生活では、画像内のオブジェクトの正確な位置に関係なく、それらを認識できるようにしたいと考えています。コンピュータビジョンは一般的な分類とは異なり、特定のオブジェクトを画像内で見つけようとするとき、特定の**パターン**やその組み合わせを探して画像をスキャンしています。たとえば、猫を探しているときは、まずひげを形成する可能性のある水平線を探し、その後、特定のひげの組み合わせがそれが実際には猫の画像であることを教えてくれます。特定のパターンの相対的な位置と存在が重要であり、画像上の正確な位置ではありません。

パターンを抽出するために、**畳み込みフィルター**の概念を使用します。ご存知のように、画像は2Dマトリックスまたは色深度を持つ3Dテンソルとして表現されます。フィルターを適用することは、比較的小さな**フィルターカーネル**マトリックスを取り、元の画像の各ピクセルに対して隣接するポイントとの加重平均を計算することを意味します。これは、画像全体をスライドする小さなウィンドウのように見え、フィルターカーネルマトリックス内の重みに従ってすべてのピクセルを平均化します。

![垂直エッジフィルター](../../../../../translated_images/filter-vert.b7148390ca0bc356ddc7e55555d2481819c1e86ddde9dce4db5e71a69d6f887f.ja.png) | ![水平エッジフィルター](../../../../../translated_images/filter-horiz.59b80ed4feb946efbe201a7fe3ca95abb3364e266e6fd90820cb893b4d3a6dda.ja.png)
----|----

> 画像提供: Dmitry Soshnikov

たとえば、MNISTの数字に3x3の垂直エッジフィルターと水平エッジフィルターを適用すると、元の画像の垂直および水平エッジがある場所にハイライト（例: 高い値）を取得できます。このように、これらの2つのフィルターはエッジを「探す」ために使用できます。同様に、他の低レベルのパターンを探すためのさまざまなフィルターを設計することができます。

> [Leung-Malikフィルターバンクの画像](https://www.robots.ox.ac.uk/~vgg/research/texclass/filters.html)

しかし、手動でパターンを抽出するためのフィルターを設計できる一方で、ネットワークをそのように設計して自動的にパターンを学習させることもできます。これがCNNの背後にある主なアイデアの1つです。

## CNNの背後にある主なアイデア

CNNの動作は、次の重要なアイデアに基づいています。

* 畳み込みフィルターはパターンを抽出できる
* フィルターが自動的に訓練されるようにネットワークを設計できる
* 元の画像だけでなく、高レベルの特徴におけるパターンを見つけるためにも同じアプローチを使用できる。したがって、CNNの特徴抽出は、低レベルのピクセルの組み合わせから始まり、画像の部分の高レベルの組み合わせに至る特徴の階層で機能します。

![階層的特徴抽出](../../../../../translated_images/FeatureExtractionCNN.d9b456cbdae7cb643fde3032b81b2940e3cf8be842e29afac3f482725ba7f95c.ja.png)

> 画像提供: [Hislop-Lynchによる論文](https://www.semanticscholar.org/paper/Computer-vision-based-pedestrian-trajectory-Hislop-Lynch/26e6f74853fc9bbb7487b06dc2cf095d36c9021d)、[彼らの研究](https://dl.acm.org/doi/abs/10.1145/1553374.1553453)に基づく

## ✍️ 演習: 畳み込みニューラルネットワーク

畳み込みニューラルネットワークの動作をさらに探求し、訓練可能なフィルターを実現する方法を理解するために、対応するノートブックを通じて作業しましょう：

* [畳み込みニューラルネットワーク - PyTorch](../../../../../lessons/4-ComputerVision/07-ConvNets/ConvNetsPyTorch.ipynb)
* [畳み込みニューラルネットワーク - TensorFlow](../../../../../lessons/4-ComputerVision/07-ConvNets/ConvNetsTF.ipynb)

## ピラミッドアーキテクチャ

画像処理に使用されるほとんどのCNNは、いわゆるピラミッドアーキテクチャに従います。元の画像に適用される最初の畳み込み層は、通常、比較的少数のフィルター（8-16）を持ち、これは水平/垂直のストロークの線のような異なるピクセルの組み合わせに対応しています。次のレベルでは、ネットワークの空間次元を減少させ、フィルターの数を増やし、これは単純な特徴のより多くの可能な組み合わせに対応します。各層で、最終的な分類器に向かうにつれて、画像の空間次元は減少し、フィルターの数は増加します。

例として、2014年にImageNetのトップ5分類で92.7%の精度を達成したネットワークVGG-16のアーキテクチャを見てみましょう：

![ImageNet層](../../../../../translated_images/vgg-16-arch1.d901a5583b3a51baeaab3e768567d921e5d54befa46e1e642616c5458c934028.ja.jpg)

![ImageNetピラミッド](../../../../../translated_images/vgg-16-arch.64ff2137f50dd49fdaa786e3f3a975b3f22615efd13efb19c5d22f12e01451a1.ja.jpg)

> 画像提供: [Researchgate](https://www.researchgate.net/figure/Vgg16-model-structure-To-get-the-VGG-NIN-model-we-replace-the-2-nd-4-th-6-th-7-th_fig2_335194493)

## よく知られているCNNアーキテクチャ

[よく知られているCNNアーキテクチャについての学習を続けてください](CNN_Architectures.md)

**免責事項**:  
この文書は、機械ベースのAI翻訳サービスを使用して翻訳されました。正確性を追求していますが、自動翻訳には誤りや不正確さが含まれる可能性があることをご理解ください。原文の母国語の文書を権威ある情報源と見なすべきです。重要な情報については、専門の人間翻訳をお勧めします。この翻訳の使用から生じる誤解や誤訳について、当社は一切の責任を負いません。