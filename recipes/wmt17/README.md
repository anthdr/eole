# Translation WMT17 en-de

---
**NOTE**
To make your life easier, run these commands from the recipe directory (here `recipes/wmt17`).
---

### Get Data and prepare

WMT17 English-German data set:

```bash
cd /eole/recipes/wmt17
apt-get install wget
bash prepare_wmt_ende_data.sh
```

### Train

Training the following big transformer for 50K steps takes less than 10 hours on a single RTX 4090

```bash
eole build_vocab --config wmt17_ende.yaml --n_sample -1 # --num_threads 4
eole train --config wmt17_ende.yaml
```

Translate test sets with various settings on local GPU and CPUs.

```bash
eole predict --src wmt17_en_de/test.src.bpe --model_path wmt17_en_de/bigwmt17/step_50000 --beam_size 5 --batch_size 4096 --batch_type tokens --output wmt17_en_de/pred.trg.bpe --gpu 0
sed -re 's/@@( |$)//g' < wmt17_en_de/pred.trg.bpe > wmt17_en_de/pred.trg.tok
sacrebleu -tok none wmt17_en_de/test.trg < wmt17_en_de/pred.trg.tok
```

BLEU scored at 40K, 45K, 50K steps on the test set (Newstest2016)

```
{
 "name": "BLEU",
 "score": 35.4,
 "signature": "nrefs:1|case:mixed|eff:no|tok:none|smooth:exp|version:2.0.0",
 "verbose_score": "66.2/41.3/28.5/20.3 (BP = 0.998 ratio = 0.998 hyp_len = 64244 ref_len = 64379)",
 "nrefs": "1",
 "case": "mixed",
 "eff": "no",
 "tok": "none",
 "smooth": "exp",
 "version": "2.0.0"
}
{
 "name": "BLEU",
 "score": 35.2,
 "signature": "nrefs:1|case:mixed|eff:no|tok:none|smooth:exp|version:2.0.0",
 "verbose_score": "65.9/41.0/28.3/20.2 (BP = 1.000 ratio = 1.000 hyp_len = 64357 ref_len = 64379)",
 "nrefs": "1",
 "case": "mixed",
 "eff": "no",
 "tok": "none",
 "smooth": "exp",
 "version": "2.0.0"
}
{
 "name": "BLEU",
 "score": 35.1,
 "signature": "nrefs:1|case:mixed|eff:no|tok:none|smooth:exp|version:2.0.0",
 "verbose_score": "66.2/41.2/28.4/20.3 (BP = 0.992 ratio = 0.992 hyp_len = 63885 ref_len = 64379)",
 "nrefs": "1",
 "case": "mixed",
 "eff": "no",
 "tok": "none",
 "smooth": "exp",
 "version": "2.0.0"
}

```
