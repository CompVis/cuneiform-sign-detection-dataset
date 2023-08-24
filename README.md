# cuneiform-sign-detection-dataset

This repository contains the dataset for cuneiform sign detection introduced in the article:
>Dencker, T., Klinkisch, P., Maul, S. M., and Ommer, B. (2020): Deep Learning of Cuneiform Sign Detection with Weak Supervision using Transliteration Alignment, PLOS ONE, 15:12, pp. 1–21
>[https://doi.org/10.1371/journal.pone.0243039](https://doi.org/10.1371/journal.pone.0243039)


The dataset comprises image references, transliterations and sign annotations of clay tablets from the [Neo-Assyrian epoch](https://en.wikipedia.org/wiki/Neo-Assyrian_Empire).


## Dataset Overview

The cuneiform sign detection dataset is based on images of over 1800 clay tablets with over 3000 view segments (inscribed side of clay tablet) and their corresponding transliterations. We provide bounding box annotations for 81 tablets with a total of 8109 annotated signs spanning over 186 sign classes.

We concentrated on the Neo-Assyrian stage of cuneiform writing (c. 900 BCE - 600 BCE), due to the amount of texts readily available and the standardized form of the cuneiform writing by that time. The dataset of clay tablet images with their transliterations mostly originates from two different sources:

- Transliterations of Neo-Assyrian tablets found in the [*State Archives of Assyria online* (SAAo)](http://oracc.museum.upenn.edu/saao/), as digitized by the ORACC project. See also the [Oracc JSON data repo](https://github.com/oracc/json).
- Clay-tablet images made available through the [*Cuneiform Digital Library Initiative* (cdli)](https://cdli.ucla.edu/).

For the Neo-Assyrian period, the SAAo series are invaluable, delivering a diverse corpus of texts, including royal correspondence, divination and literary texts or international treaties, among other genres.

## Dataset Compilation

We distilled our dataset by first extracting the usable transliterations from SAAo and then collating those with the available images in cdli. Only those tablets having both a usable transliteration and an image were included in our corpus.

A clay tablet image, as available at cdli, is composite image composed of images of the various inscribed sides (usually front and back, sometimes upper and lower or left, right sides of the clay tablet).
Similarly the transliterations are organized according to the visible sides of the clay tablet.
To compile this dataset we first segmented the composite images and then matched the resulting view segments with their transliteration sides.

Both steps of the pre-processing were performed algorithmically and thus contain errors which need to be taken into account when working with the provided dataset.
In particular the matching step (segment assignment) relies on several heuristics, since composite images of clay tablets differ in their number of segments and in their layout.

In order to obtain sign annotations we employed Assyriology graduate and PhD students that used a custom annotation tool to draw bounding boxes and label individual cuneiform signs. We follow the popular sign code classification (`mzl_label`) established by Borger's [Mesopotamisches Zeichenlexikon (MZL)](#mzl) which hopefully makes the dataset more accessible to other researchers.


## Dataset Organization

The dataset is organized in terms of *collections* of clay tablets.
For the most part we follow the organization of SAAo and only deviate in the train and test collections.
In total we provide ten collections with eight from SAAo and two custom collections (with clay tablets from multiple sources):

- saa01, saa05, saa06, saa08, saa09, saa10, saa13, saa16
- train, test

This dataset is stored in csv files that contain all the information for the individual collections. We provide the following csv files for all collections:
- [tablet segment metadata](./segments/)
- [transliterations](./transliterations/)

The annotation csv files are only provided for a smaller subsets of collections:
- [bbox annotations](./annotations/)
- [line annotations](./annotations/)

For image data we rely on the [cdli website](https://cdli.ucla.edu/) where the tablet composite image files are made available. The individual image files are accessed by means of their cdli no. which we provide in the csv files. For a code example how to load a tablet image and visualize the data provided in this dataset, please consult our [short introduction jupyter notebook](Cuneiform_Sign_Detection_Dataset_Introduction.ipynb).

While the vast majority of tablet images from our dataset is available on the cdli website, seven tablet images from the collection of the [Vorderasiatisches Museum Berlin](https://www.smb.museum/en/museums-institutions/vorderasiatisches-museum/home.html) are currently not available. Kindly the museum granted us the permission to provide them for research purposes as a downloadable ZIP archive [here](https://ommer-lab.com/files/cunei/VAT_train_images.zip).

## Details on the Dataset Composition

| Set | BBoxes | Signs | Segments | Tablets | Sources |
|-----|:---:|------|:-----:|:-----:|:-----:|
| train_full (Train BB) | Yes | 4663 | 67 |  47 | train(mixed), saa05, saa09 |
| test_full (Test) | Yes | 3446 | 57 |  34 | test(mixed, saa03), saa06 |
| transliteration (Train TL) | No | 185399 | 2983 |  1745 |  saa01, saa05, saa08, saa10, saa13, saa16 |

IMPORTANT: These statistics only take into account signs with train_labels in the range (1<=x<=240) and in the case of transliterations are not marked as broken. Label 0 indicates background annotations.


## Details on the CSV Files

The *tablet segment metadata* csv files contain the following information:

- tablet cdli no.
- segment index
- bounding box annotation of the tablet segments in the composite image
- assignment of views to the segments (e.g. obverse, reverse)
- estimated scale factor that aims for an average sign height of about 128 image pixels
- collection name

The *transliteration* csv files contain the following information:

- tablet cdli no.
- segment index
- line by line *cuneified* transliteration (we removed the additional information available in a transliteration)
- entries are index by line and position in line
- sign label according to MZL
- sign label for machine learning according to mapping in label.json

The *bbox annotation* csv files contain the following information:

- tablet cdli no.
- segment index
- bounding box coordinates relative to segment bbox and full composite image
- sign label according to MZL
- sign label for machine learning according to mapping in label.json

The *line annotation* csv files contain the following information:

- tablet cdli no.
- segment index
- lines that are defined by the start and end points of segments with (x,y) coordinates
- line index identifies the points belonging to a line (lines can consist of one or several connected segments)


The columns **tablet cdli no. & segment index** are used as *unique key* across the different csv files that allows to identify the records belonging to a specific tablet segment.


## Helpful Links

- [list of cuneiform signs on wikipedia](https://en.wikipedia.org/wiki/List_of_cuneiform_signs)

## References

- <a id='saa'>S. Parpola et al., State archives of Assyria, Vol. I-XX, Helsinki University Press, 1987-2015.</a>
- <a id='mzl'>R. Borger, Mesopotamisches Zeichenlexikon, Bd. 305. Münster: Ugarit-Verl., 2004.</a>
