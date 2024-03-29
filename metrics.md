# Segmentation metrics overview

| Metric                  | Needs ref             | Boundary types     | Near misses        | Different sequences   | Implementation |
|-------------------------|-----------------------|--------------------|--------------------|-----------------------|----------------|
| F1                      | :heavy_check_mark:(1) | :x:                | :x:                | :x:                   |                |
| Pk                      | :heavy_check_mark:(1) | :x:                | :heavy_check_mark: | :x:                   | [SegEval](https://pypi.org/project/segeval/) |
| WindowDiff              | :heavy_check_mark:(1) | :x:                | :heavy_check_mark: | :x:                   | [SegEval](https://pypi.org/project/segeval/) |
| Segmentation Similarity | :heavy_check_mark:(1) | :heavy_check_mark: | :heavy_check_mark: | :x:                   | [SegEval](https://pypi.org/project/segeval/) |
| Boundary Similarity     | :heavy_check_mark:(1) | :heavy_check_mark: | :heavy_check_mark: | :x:                   | [SegEval](https://pypi.org/project/segeval/) |
| BLEU(-br)               | :heavy_check_mark:(n) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:    | [SacreBLEU](https://github.com/mjpost/sacrebleu)? | 
| TER-br                  | :heavy_check_mark:(n) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:    | [TER](https://www.cs.umd.edu/~snover/tercom/)        |
| S-BLEU | :heavy_check_mark:(n) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | [SacreBLEU](https://github.com/mjpost/sacrebleu) | 


### Pk

Beeferman99statistical

Measures the probability that two units *k* steps apart are incorrectly labeled as being in different segments.
Is calculated by setting *k* to half of the average true segment size and then computing penalties via a moving window of length *k*.
At each location, the algorithm determines whether the two ends of the probe are in the same or different segments in the reference segmentation and increases a counter if the algorithm’s segmentation disagrees.
The resulting count is scaled between 0 and 1 by dividing by the number of measurements taken.

![formula](https://render.githubusercontent.com/render/math?math=P_k(hyp,ref)=\frac{1}{N-k}\sum_{i=1}^{N-k}\delta(f(hyp,i,i%2Bk)%20\ne%20f(ref,i,i%2Bk)))

### WindowDiff

[Pevzner02critique](https://direct.mit.edu/coli/article-pdf/28/1/19/1797682/089120102317341756.pdf)

For each position of the window, compares the number of reference segmentation boundaries that fall in this interval (*b(ref,i,i+k)*) with the number of boundaries that are assigned by the algorithm (*b(hyp,i,i+k)*).
The algorithm is penalized if *b(ref,i,i+k)* != *b(hyp,i,i+k)*

![formula](https://render.githubusercontent.com/render/math?math=WD_k(hyp,ref)=\frac{1}{N-k}\sum_{i=1}^{N-k}\delta(b(hyp,i,i%2Bk)%20\ne%20b(ref,i,i%2Bk)))

### Segmentation Similarity

[Fournier12segmentation](https://www.aclweb.org/anthology/N12-1016.pdf)

Proportion of boundaries that are not transformed (added/deleted, substituted) when comparing them using edit distance (transposition allowed up to *n* steps).

![formula](https://render.githubusercontent.com/render/math?math=S(s_a,s_b,n)=1-\frac{d(s_a,s_b,n)}{N-1})

### Boundary Similarity

[Fournier13evaluating](https://www.aclweb.org/anthology/P13-1167.pdf)

New weights and new normalization for boundary edit distance.
Assuming that boundary edit distance produces sets of edit operations where *A* is the set of additions/deletions, *T* the set of *n*-wise transpositions, *S* the set of substitutions, and *M* the set of matching boundary pairs, boundary similarity can be defined as:

![formula](https://render.githubusercontent.com/render/math?math=B(s_a,s_b,n)=1-\frac{|A|%2Bw_t^{span}(T,n)%2Bw_s^{ord}(S,n)}{|A|%2B|T|%2B|S|%2B|M|})

### BLEU(-br)

[karakanta2042](https://www.aclweb.org/anthology/2020.iwslt-1.26.pdf)

BLEU computed with the data containing breaks as special symbols. Each break symbol counts as an extra token that contributes to the score.

### TER-br

[karakanta2042](https://www.aclweb.org/anthology/2020.iwslt-1.26.pdf)

TER calculated with all tokens of the sentence masked.

### S mode BLEU (S-BLEU)

[Matusov19customizing](https://www.aclweb.org/anthology/W19-5209.pdf)

Subtitle BLEU. Calculates BLEU on subtitles instead of sentences, so that any target words that appear in the wrong subtitle count as error. Assumes that the subtitles in the target and the reference match.

### Conformity to the subtitle constraint of length (CPL_conf)
Subtitles should not exceed a specific length. Conformity is measured as a maximum subtitle length of _n_ characters per line (maximum 2 lines of up to _n_ characters each for the subtitle block), where _n_ is 42 according to TED subtitling guidelines. CPL_conformity is the percentage of subtitles in the corpus conforming to the length constraint.

# Adapting standard metrics via alignment

MWER (Matusov et al., 2006)
