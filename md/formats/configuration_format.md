# Configuration Format

## Contents

* [Decoding](#decoding).
* [Training](#training).

## Decoding

The following shows a sample configuration file for decoding: [config_decode.xml](https://github.com/clir/clearnlp/blob/master/src/main/resources/configure/config_decode.xml).

```
<configuration>
    <language>english</language>

    <reader type="raw"/>

    <global>
        <distributional_semantics>brown-rcv1.clean.tokenized-CoNLL03.txt-c1000-freq1.txt.xz</distributional_semantics>
        <distributional_semantics>model-2030000000.LEARNING_RATE=1e-09.EMBEDDING_LEARNING_RATE=1e-06.EMBEDDING_SIZE=100.txt.xz</distributional_semantics>
        <distributional_semantics>hlbl_reps_clean_2.50d.rcv1.clean.tokenized-CoNLL03.case-intact.txt.xz</distributional_semantics>
        <named_entity_dictionary>general-en-ner-dict.xz</named_entity_dictionary>
    </global>

    <model>
        <pos>general-en-pos.xz</pos>
        <dep>general-en-dep.xz</dep>
        <ner>general-en-ner.xz</ner>
    </model>

    <dep>
        <root_label>root</root_label>
        <beam_size>1</beam_size>
    </dep>
</configuration>

```
	
The following describes the specification of each element.

| Element | Description |
| :-----: | :---------- |
| `<language>` | Specifies the language of the models.<ul><li>See [TLanguage](https://github.com/clir/clearnlp/blob/master/src/main/java/edu/emory/clir/clearnlp/util/lang/TLanguage.java) for all supported languages.</li></ul> |
| `<model>` | Specifies the model file of each component.<ul><li>See [NLPMode](https://github.com/clir/clearnlp/blob/master/src/main/java/edu/emory/clir/clearnlp/component/utils/NLPMode.java) for all supported components.</li><li>See [How to add models](../quick_start/models.md) for more details about the model files.</li></ul> |
| `<reader>` | Specifies the data format of the input files.<ul><li>`type` specifies the type of the [data format](../formats/data_format.md):<br>&#9702; `raw`: accepts texts in any format.<br>&#9702; `line`: requires each sentence to be in one line.<br>&#9702; `tsv `: requires each field to be in one column delimited by tabs.</li><li>When `tsv ` is used, `<column>` must be specified.<br>&#9702; `index` specifies the index of the field, starting at 1.<br>&#9702; `field` specifies the name of the field.<br>&nbsp;&nbsp;&nbsp;&#8226; `id`: token ID.<br>&nbsp;&nbsp;&nbsp;&#8226; `form`: word form.<br>&nbsp;&nbsp;&nbsp;&#8226; `lemma`: lemma.<br>&nbsp;&nbsp;&nbsp;&#8226; `pos`: part-of-speech tag.<br>&nbsp;&nbsp;&nbsp;&#8226; `feats`: extra features.<br>&nbsp;&nbsp;&nbsp;&#8226; `headId`: head token ID.<br>&nbsp;&nbsp;&nbsp;&#8226; `deprel`: dependency label.<br>&nbsp;&nbsp;&nbsp;&#8226; `sheads`: semantic heads.<br>&nbsp;&nbsp;&nbsp;&#8226; `nament`: named entity tag.</li></ul> |
| `<dep>` | Specifies the configuration of dependency parsing.<ul><li>`root_label`: label of the root node.</li></ul> |

## Training

* [Part-of speech tagging](../components/pos_tagging.md#configuration).

The following shows a sample configuration file for training: [config_train.xml](https://github.com/clir/clearnlp/blob/master/src/main/resources/configure/config_train.xml).

```
<configuration>
    <language>english</language>
    
    <reader type="tsv">
        <column index="1" field="id"/>
        <column index="2" field="form"/>
        <column index="3" field="lemma"/>
        <column index="4" field="pos"/>
        <column index="5" field="feats"/>
        <column index="6" field="headId"/>
        <column index="7" field="deprel"/>
        <column index="8" field="sheads"/>
        <column index="9" field="nament"/>
    </reader>

    <pos>
        <trainer algorithm="adagrad" type="svm" labelCutoff="4" featureCutoff="3" alpha="0.02" rho="0.1" average="false"/>
        <ambiguity_class_threshold>0.4</ambiguity_class_threshold>
        <proper_noun_tagset>NNP,NNPS</proper_noun_tagset>
        <bootstraps>true</bootstraps>
    </pos>
    
    <dep>
        <trainer algorithm="adagrad" type="svm" labelCutoff="4" featureCutoff="3" alpha="0.04" rho="0.1" average="false"/>
        <evaluate_punctuation>false</evaluate_punctuation>
        <root_label>root</root_label>
        <bootstraps>true</bootstraps>
    </dep>
</configuration>
```

The following describes the specification of each element.

| Element | Description |
| :-----: | :---------- |
| `<dep>` | Specifies the configuration of dependency parsing.<ul><li>`evaluate_punctuation`: if `true`, evaluate punctuation during development.</li><li>`root_label`: label of the root node.</li></ul> |
| `<bootstraps>` | if `true`, use bootstrap iterations for training sequences. | 

`<global_lexica>` specifies the lexicons used globally across different components.

 * `distributional_semantics`: distributional lexicons (e.g., brown clusters, word embeddings).
 * `named_entity_dictionary`: named entity dictionary.

`<trainer>` specifies the training algorithm and its parameters.

 * `algorithm`: `adagrad` for AdaGrad, `liblinear`for Liblinear.
 * `type`: `svm` for hinge loss classification, `lr`for logistic regression.
 * `labelCutoff`: count threshold for labels appearing less than `N` times.
 * `featureCutoff`: count threshold for features appearing less than `N` times.
 * `average`: if `true`, apply averaging to online learning.</li><li>`alpha`: learning rate (AdaGrad).
 * `rho`: ridge to keep the inverse covariance well-conditioned (AdaGrad).