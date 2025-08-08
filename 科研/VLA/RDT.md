/home/c6h4o2/文档/models
ln -s /home/c6h4o2/document/models/t5-v1_1-xxl google/t5-v1_1-xxl

```
python -m eval_sim.eval_rdt_maniskill \
--pretrained_path ./mp_rank_00_model_states.pt
```
python ./eval_sim/eval_rdt_maniskill.py --pretrained_path mp_rank_00_model_states.pt 