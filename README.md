# BHI100 SISR IQA Metrics

This is an interactive comparison table of single image super resolution model iqa metric scores.

I downloaded and ran local inference on my [BHI100 set](https://huggingface.co/datasets/Phips/BHI100), then calculated iqa metric scores with [pyiqa](https://github.com/chaofengc/IQA-PyTorch) 

Find the interactive table at [https://phhofm.github.io/bhi100-sisr-iqa-metrics/](https://phhofm.github.io/bhi100-sisr-iqa-metrics/)   

Columns are sortable, so you can sort by metric score or by model name. Use arrow keys to scroll the table horizontally more easily.    

![image](https://github.com/user-attachments/assets/2efecfd7-0d21-4c4a-9a6f-fd18acc30d78)
<sub>Screenshot of interactive table / github page</sub>


This data can of course also be used to visualize data for example with charts, here is an example bar chart of official 4x paper pretrain model scores on the dists metrics:
![dists](https://github.com/user-attachments/assets/a29c4c8b-de66-4da2-a574-5fb3ff98f527)
<sub>Dists metric scores of some released sisr paper pretrains</sub>


More charts can be found in [the charts folder](https://github.com/Phhofm/bhi100-sisr-iqa-metrics/tree/main/charts)


### Motivation

Training sisr models myself, I ran into some circumstances that got on my nerves.

1. Official sisr validation sets (Set5, Set14, BSD100, Urban100, Manga109 ..) have weird image dimensions in their HR that are not always evenly divisible by downsample scales (2, 3 or 4) which leads to different results, for example img004 from Urban100 with 1024x681px led to different results if bicubic downsampling had been done with pillow, or with matlab. So there is no uniformity concerning image dimensions.

To remedy this, I created my [BHI100 validation set](https://huggingface.co/datasets/Phips/BHI100), where all the HR are 480x480px which is easily divisible by 2, 3 and 4, and I released these downsampled lr sets with the dataset so all can be tested on the exact same set/images.

2. I was not always able to reproduce reported paper metrics from networks on previous reported sets like Urban100, maybe they used a different model then the one they released, maybe something is just whack and not all comparisons are fair. I noticed different things, first that even though "psnr" is stated in the papers, they actually used "psnry". "psnr" gives lower scores than "psnry" in general. If one of these papers actually used "psnr" and another used "psnry", then the "psnry" will have an advantage/look better in comparison. Not only can this be confusing, but psnr and ssim are rather old metrics, and [belong to the 5 worst FR metrics as seen in this benchmark on the pyiqa repo](https://github.com/chaofengc/IQA-PyTorch/blob/main/tests/FR_benchmark_results.csv), but are still the two metrics used in all the sisr benchmarks. Why not also report on others like topiq_fr, vif and dists?

To remedy this, I downloaded the sisr models myself (like official pretrains from network repos, or then community models later), then ran inference on my BHI100 set to create the model outputs, and then used pyiqa to score them all on multiple metrics which I am releasing here, so these models scores can be more fairly compared based on their released/official model performance.
