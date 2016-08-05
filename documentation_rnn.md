# Torch and TorchRNN Documentation

This documentation explains how to preprocess a file and pass it into a neural net, how to quickly generate convergence plots of this, and how to visualize/create your own model, do hallucination side-by-side test with these and so on. I will include a [section](#Torch) on Torch/recommended tutorials for this, but these will be fairly brief.

On the Cortex dropbox, I have put all the trained, useful neural nets inside the folder `UsefulNets`, there is a Readme.todo file there that describes what these nets where trained for/most folders have .sh files that were used to run them.

## Torch

As from their [website](http://torch.ch/), Torch is a scientific computing framework with wide support for machine learning algorithms that puts GPUs first. It is easy to use and efficient, thanks to an easy and fast scripting language, LuaJIT, and an underlying C/CUDA implementation.

Essentially, Torch is the workhorse of modern fast machine learning. If you are interested I recommend the following tutorials, though [torch's page](https://github.com/torch/torch7/wiki/Cheatsheet) does a great job recommending some. Note that learning 

- [Basic tutorial](https://github.com/soumith/cvpr2015/blob/master/Deep%20Learning%20with%20Torch.ipynb)
- [Architecture tutorial](https://github.com/soumith/cvpr2015/blob/master/NNGraph%20Tutorial.ipynb)
- [Facebook use of torch, more advanced](https://github.com/soumith/cvpr2015/blob/master/cvpr-torch.pdf)

## Char RNN and Modifications

[CharRNN](https://github.com/karpathy/char-rnn) is a good package that has simple LSTM/RNN's for training and character level use. The models are all created with the [nngraph](https://github.com/torch/nngraph) package, which means that you can visually see the networks very well. The downside of using char is possibly speed, but moreso it is how it sets up parameters. I have tried to tweak this to fix this, to varying degrees of success.

We have a couple of packages using char, all located in the `/home/euler/RNN` folder.

When CharRNN runs, it saves checkpoints in t7 files, which will contain all your validation losses so you can generate a convergence plot. You **must** open these files on tor (you can do this by opening up `th`, and then typing `dat= torch.load(file_name`). The script `loadFile.lua` will open the given checkpoint file and export the validation losses in csv format so you can quickly graph them. Eg: `th loadFile.lua -input_file cv/my_checkpoint.t7 -save_file my_output.csv`.

### 1. Char-RNN-Master

This is just the original CharRNN. No processing is necesary, you can just run train.lua with `th train.lua`. Things to note on this are:

- Tor's GPU is large, the default sequence length is 50, use something much higher, you can check the RAM usage of this via `nvidia-smi`.
- Similarly, batching will help speed up the training itself.
- This saves checkpoints, you can reload these nicely.

### 2. newcharrnn

This is my [modified CharRNN](https://iguanaus@bitbucket.org/iguanaus/newcharrnn.git). This has the LayerCake architecture, custom size per layer LSTM, iggynet (RNN), and iggynet (LSTM). The IggyNet is the idea that Henry proposed of having the inputs fed through 10 baby neural nets, and then these outputs concatenated and fed into a large neural net. This may actually still have promise. Leon has access to this repository. Also, this code auto-generates the csv's of the validation losses. 

To run these different architectures, just select a different setting for `model`. You can open `train.lua` to see specifically the options. 

Note that this script can also generate flow charts for neural nets - based upon nngraph. To turn this on set the line 223 of the code from false to true, then change the input dimension to be the same as it should be (for an RNN it should be 1+layernumber, and for an LSTM it should be 1+ 2*layernumber). After you generate the map (it should save it as a .svg file), you need to run displaySVG.py, this will save it as a PDF and automatically open it. 

Note that the losses from this are still in nats, so make sure to convert them to bits. I could not change it from nats to bits or else a lot of our graphs would have been very confusing, and if we updated/recreated a package anywhere it could cause problems. 

To generate an entropy file, run the script `entropyCalc.lua`, and simply initialize the checkpoint/chorrect data files. An example of this running can be seen in the `calcEntropyCalc.sh` script.

To generate a side-by-side comparison test (eg if you wanted to see how close the model is to generating the same text), run the script `hallucinateTest.lua` with the correct initialization file/data files. An example of this is the script `runHalluc.sh`.

### 3. LSTM-char-cnn

[This](https://github.com/yoonkim/lstm-char-cnn) code is based upon this [paper](http://arxiv.org/abs/1508.06615). We were using it for benchmark comparison with the IggyNet. It is currently ongoing developement.

## Torch RNN and Modifications

[TorchRNN](https://github.com/jcjohnson/torch-rnn) is a more complicated package (because it does not use nngraph, so debugging the code is very difficult), but also possibly faster. 

When torch 

### 1. torch-rnn-master

Similarly to above, this is the original torchRNN. Preprocess with the script preprocess.py, and then train with `th train.lua`. It is pretty plug and play, bear in mind the output is in nats.

### 2. torch-swat

Pretty much the same as [torch-rnn-master](#torch-rnn-master).

### 3. torchrnnmodified

[This](https://bitbucket.org/iguanaus/torchrnnmodified) is a heavily modiifed version of torch-rnn. Leon has access to the repository. To normally run this (on characters), just use `prep_compress.py`, otherwise use the `prep_compress_integers.`.

#### Exporting 

To export a neural network, use the flag `-out 1`, and it will save them into a parameters folder. **Note that you must manually go down to around line 280, and pick which matrices need to get saved. This is pretty generic, except there is a jump of 2 from the last layer to the linear output).
The output of these matrices is well-documented in the `UsefulNets\ReadMe.txt`

#### Importing

Similarly, you can pass a flag for `-init_wieghts_from` to initialize from a given folder of wieghts after modifying them. Note you need to go modify the loop to make sure the correct files get loaded around line 300.

You can also run the `hallucinateTest.lua` which will do a side-by-side, and the `torchEntropyCalc.lua` to generate entropies.

### 4. torchrnn_hutter

This is designed for the LPAQ models/bit predictions. To prepare the file, run `prep_compress_continuousnumbers.py` with the correct settings. This will take in a model/bit and run it. **Note: you need to watch out for the offset**. After you do this, run it with the file `trainTry3.lua`.

### 5. torchrnnmulti

This is the architecture for taking in the first four characters, and predicting the next word's first four chracters. Run the precompress with the script `prep_compress_parr.py`, and then train with `trainTry3.lua`. An example of this is the script `runTruncateParralel.sh`.














