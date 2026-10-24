# T4 验收留证：Phi-4-mini-instruct serve 拉起（2026-10-22）

## 1. 拉起命令

```bash
export ASCEND_RT_VISIBLE_DEVICES=0
export PHI4_MODEL_PATH=/root/.cache/huggingface/hub/models--microsoft--Phi-4-mini-instruct/snapshots/cfbefacb99257ffa30c83adab238a50856ac3083
vllm serve $PHI4_MODEL_PATH --served-model-name Phi-4-mini-instruct --port 8123 --dtype bfloat16 --max-model-len 4096 --enforce-eager --gpu-memory-utilization 0.8
```

## 2. 版本信息
[W1022 17:31:59.287374770 FunctionLoader.cpp:48] Warning: LD_PRELOAD detected, FunctionLoader prefers RTLD_DEFAULT for symbol resolution. (function operator())
torch 2.10.0+cpu / torch_npu 2.10.0.post4
+---------------------------+---------------+----------------------------------------------------------------------+

## 3. curl 请求验证（/health 与 chat completions）

- `curl http://127.0.0.1:8123/health` → HTTP 200
- 中文请求（acceptance_curl_zh.json）："用一句话解释重力。" → "重力是一个物体由于其质量产生的引力，吸引其他物体。"（21 tokens，finish_reason=stop）
- 英文请求（acceptance_curl_en.json）："Explain gravity in one sentence." → "Gravity is the force by which a planet or other body draws objects toward its center, ..."（finish_reason=stop）

## 4. aisbench gsm8klite（100 样本，temperature=0，stream API）

| dataset | metric | accuracy |
|---|---|---|
| gsm8klite | gen | **86.00** |

- 日志: aisbench_run.log；明细: aisbench_gsm8klite/20261022_174649/（infer .out / summary txt/csv/md）
- 100/100 发送、100/100 接收、0 失败、742s
