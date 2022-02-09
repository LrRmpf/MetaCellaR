# MetaCellaR
MetaCellaR is an Rscript to summerize single cells based on k-medoids. The clustering is done per cell type. We refer to these clusters as metacells.
Essentially, we compute the center of each metacell (by averaginf the gene expression profiles of cells that are grouped together into the same cluster) and consider this as the expression profile of a metacell. The gif below illustrates this procedure:
![ Alt text](metacellar_gif.gif) [](metacellar_gif.gif)

The input arguments to our MetaCellaR script are:
- Use the `-file` argument followed by a file path to where the single cell, either a csv file or a Seurat object, is stored. In case of the csv file, the rows and columns must be annotated by gene names and cell names, respectively.
- In case of a csv input file explained above, the `-celltype` argument must be a 2-column table containing the mapping for cells to their cell types. First column must be the same as cell names provided in the csv file, and second column the cell types. In case of the Seurat object, the `-celltype` argument must be used to refer to the slot where the cell type annotation of single cells are stored in the Suerat object. For instance, if the full way to access the cell types stored in a Seurat object called `S` is to call `S@meta.data$celltype` then the user needs to type in `-celltype meta.data$celltype` to indicate that the cell type annotations for the single cell gene expression data are accessible via those slots/items.
- `-RNA` is used only for the Seurat object. This argument refers to the slot where the RNA counts are stored in the Suerat object. For instance, if the full way to access the RNA counts stored in a Seurat object called `S` is to call `S@assays$RNA@counts` then the user needs to type in `-RNA assays$RNA@counts` to indicate that the single cell gene expression counts are accessible via those slots/items.
- `-ATAC` is used only for the Seurat object. This argument refers to the slot where the ATAC counts are stored in the Suerat object. For instance, is the full way to access the ATAC counts stored in a Seurat object called `S` is to call `S@assays$ATAC@counts`, then the argument passed to `-ATAC` should be 'assays$ATAC@counts'.
- Argument `-k` can be used to specify the number of clusters for the k-medoids clustering. If not provided by the user, `k` is defined based on the number of potential groups that have at least 30 cells inside (k <- #cells in a cell type / 30)
- Use the `-assay` argument followed by the slot where the assay information are stored. The goal is to tell MetaCellaR where the cells from the scRNA-seq data should be accessed to compute the metacells. The valid names to refer to the scRNA-seq assay are (case insensitive): "scrna-seq", "scrna", "scrna", and "rna".
- `-umap` is a boolean argument which if set to TRUE will project the RNA expression counts to a UMAP space of 20 components. These components are then used to generate the clusters. Setting this argument to TRUE will substantially *increase* the runtime *speed*.
- `e` allows the user to set the number of expected cells per metacell. The defaults is 30. A greedy algorithm is run, after clustering, to merge the low cell count metacells (those that have fewer cells than this expected numberi) to reach to the this desired value.
- To specify an output directory in where the results will be stored, the user can supply the `-output` argument.
- The k-NN implementation requires a capping threshold, `-t`, on the number of ATAC cells assigned to a metacell. If this number exceeds the threshold, the algorithm will pick the next closest metacell for labeling. The default value is 3 * `e`. This option should be used only when the ATAC data is availbe.
