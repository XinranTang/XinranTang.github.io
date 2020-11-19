---
title: "Video Features Extraction Using DL"
published: true
---


# Scene Extraction

#### 1. DataLoader:

https://pytorch.org/docs/stable/data.html

``` Python
DataLoader(dataset, batch_size=1, shuffle=False, sampler=None,
           batch_sampler=None, num_workers=0, collate_fn=None,
           pin_memory=False, drop_last=False, timeout=0,
           worker_init_fn=None, *, prefetch_factor=2,
           persistent_workers=False)
```

**Dataset** – It is mandatory for a DataLoader class to be constructed with a dataset first. PyTorch Dataloaders support two kinds of datasets:

    Map-style datasets – These datasets map keys to data samples. Each item is retrieved by a __get_item__() method implementation.

    Iterable-style datasets – These datasets implement the __iter__() protocol. Such datasets retrieve data in a stream sequence rather than doing random reads as in the case of map datasets.

**Batch size** – Refers to the number of samples in each batch.

**Shuffle** – Whether you want the data to be reshuffled or not.

**Sampler** – refers to an optional torch.utils.data.Sampler class instance. A sampler defines the strategy to retrieve the sample – sequential or random or any other manner. Shuffle should be set to false when a sampler is used.

**Batch_Sampler** – Same as the data sampler defined above, but works at a batch level.

**num_workers** – Number of sub-processes needed for loading the data.

**collate_fn** – Collates samples into batches. Customized collation is possible in Torch.

**pin_memory** – Pinned (page-locked) memory locations are used by GPUs for faster data access. When set to True, this option enables the data loader to copy tensors into the CUDA pinned memory.

**drop_last** – If the total data size is not a multiple of the batch_size, the last batch has less number of elements than the batch_size. This incomplete batch can be dropped by setting this option to True.

**timeout** – Sets the time to wait while collecting a batch from the workers (sub-processes).

**worker_init_fn** – Defines a routine to be called by each worker process. Allows customized routines.