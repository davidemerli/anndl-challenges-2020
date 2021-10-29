# Image segmentation

Given four image datasets identified by the team that acquired the data, in our second homework we decided to focus on the datasets with the most similar images.

Therefore, in our attempts we primarily used ‘Bipbip’ and ‘Weedelec’ images together, of both maize and haricot; we also tried considering all available images but with worse results and very high training times.

We implemented both a transfer learning approach (with different networks like VGG, MobileNet and DenseNet), and custom networks (simplified U-net typology), achieving better results with the latter ones. We decided to split the dataset in 90% for training and 10% for validation and we created a dictionary which associates each image to its mask.

The evaluation metric considered for semantic segmentation is Intersection Over Union (IoU, also known as Jaccard index). We tested several preprocessing functions to perform image augmentation, both from already existing networks and customized; our best results are obtained exploiting ‘densenet’ or ‘imagenet_utils’ preprocessing applied after our soft custom augmentation, or, in alternative, with only our custom augmentation (therefore without further preprocessing).

We exploited ‘ReducingLROnPlateau’ class to train with a decreasing learning rate, starting from 1e-3 value to 1e-6, obtaining in this way better performances.
We initially considered entire images, noticing worse results on test set’s predictions with smaller input image sizes supplied to our network; obviously, this was due to a reduced image resolution.

By increasing the input image size we improved the prediction score on the test set. In this way, we obtained two relevant results: one (‘ANN_Second_Challenge_a’ notebook) considering both ‘Weedelec’ and ‘Bipbip’ team’s images and supplying a slightly reduced image size (i.e. 1024x1024) to the network, allowed us to get 0.657 as IoU on Bipbip dataset, 0.687 on Weedelec and 0.52 as Global IoU; the second one (‘ANN_Second_Challenge_b’ notebook) which considers only ‘Bipbip’ team’s images, using as image input size the original resolution (2048x1536) of the considered Dataset.

This allowed us to reach 0.732 as IoU metric on Bipbip and 0.36 as Global IoU. As expected, the latter one has obtained a lower performance on Global IoU since only the images provided by one team were considered in the training phase. These results were achieved with an U-net like network, with less parameters than the original one, considering adequate filter sizes (see notebooks). Moreover, BatchNormalization is exploited to make the network faster and more stable. Finally, after several trials, the batch size which allowed us to reach our bests is equal to 4.

At this point we decided to apply tiling technique on Bipbip dataset. We split each image and relative mask into 48 tiles, obtaining smaller images of 256x256 size: in this way we did not decrease the resolution of our original images, but instead we maintained very detailed masks. We applied tiling on the test set too, and after prediction was performed on test’ tiles, we merged the resulting masks. We reassembled them in order to obtain a single mask for each original test image (‘ANN_Second_Challenge_c’ notebook). Here was used a batch size of 32.

We did not achieve better results by tiling the images. We noticed that the model was capable of identifing the plants from the background, but failed to differentiate from crops to weeds, probably because of the lack of information of the surroundings due to the tiling itself. Looking at output masks it is almost clearly distinguishable where the tiling was done, since there are even region delimited by straight lines.
