
Clean way to have different python environments.

https://www.anaconda.com/download/

Description | Command
--- | ---
List environments|```conda info --envs``` 
Add and environment|```conda create --name myname python=3.5 astroid babel otherpackages```
Activate environment|```source activate myname```
Clone environment|```conda create --name mynewname --clone oldname```
Remove environment|```conda remove --name myenvname --all```

conda install --channel https://conda.anaconda.org/pandas bottleneck
