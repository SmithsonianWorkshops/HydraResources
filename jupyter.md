# Jupyter on Hydra

Jupyter notebooks deliver a "literate programming" interface with Python that combines MarkDown-based documentation with code cells.

Using Jupyter on Hydra is especially helpful for:
* Keeping a record of a your analysis in notebook format, that can be accessed later
* Working with images or visualizations that cannot be viewed through the command line interface.

From the log-in node, access the interactive queue.

```console
qrsh -pe mthread [N]
```

...where N is the number of threads you would like to request.

*If you have access to the GPU nodes, you can use* `qrsh -l gpu`.

You will notice that your prompt will change from `[user@hydra-login01]` to `[user@computer-XX-XX]` where *XX-XX* is the name of the Hydra compute node you are now on.

Then load the conda module, and activate a specific module with jupyter, if necessary.

```console
module load ~/modulefiles/miniconda
conda activate jupyter
```

*If you have access to the GPU nodes, you will also need to run* `module load cuda10.0` *to load the CUDA libraries. You can test that it worked with the command* `nvidia-smi`.

Now run the following command to launch a jupyter notebook. It will run it from the directory you are in, so make sure you are "above" the directory where you want your notebooks to live. The interactive queue puts you into your `/home` directory by default.

```console
 jupyter notebook --no-browser --ip=`hostname` --port=8888
```

This will launch a notebook, and produce a bunch of output looking something like this:

```
[I 14:38:44.628 NotebookApp] Serving notebooks from local directory: /pool/genomics/triznam
[I 14:38:44.628 NotebookApp] The Jupyter Notebook is running at:
[I 14:38:44.628 NotebookApp] http://compute-8-31.local:8888/?token=e54bd4f1469387555c114697278fe2ff10089cbf723c595b
[I 14:38:44.628 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 14:38:44.636 NotebookApp]

    To access the notebook, open this file in a browser:
        file:///home/triznam/.local/share/jupyter/runtime/nbserver-25198-open.html
    Or copy and paste one of these URLs:
        http://compute-8-31.local:8888/?token=e54bd4f1469387555c114697278fe2ff10089cbf723c595b

```

Now on your local computer, open up a terminal, and run the following command:

```console
ssh -N -L 8888:compute-XX-XX:8888 triznam@hydra-login01.si.edu
```

...where *XX-XX* is the compute node that the `qrsh` command placed you. The first 8888 is the port of your local computer. If you are already running a local Jupyter notebook, then simply change this to a free port. The second 8888 is the port of the compute node, so change this if you used a different port in the `jupyter notebook` command above.

If it worked, it will prompt you for your Hydra password. Enter it.

Now open a browser and go to http://localhost:8888. It should be a Jupyter-looking window, but will ask for a token or password before you can continue. Copy and paste the long token from the end of the URL that was printed out when you launched Jupyter on Hydra above. In that case it would be: *e54bd4f1469387555c114697278fe2ff10089cbf723c595b*


