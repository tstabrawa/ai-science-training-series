# Sambanova

## Connection to Sambanova 

Connection to a SambaNova node is a two-step process. The first step is to `ssh` to the `login node`. The second step is to log in to a SambaNova node from the `login node`.

![Sambanova connection diagram](./sambanova_login.jpg)

Login to the Sambanova login node from your local machine.  This uses the **MobilePASS+** token generated every time you log in to the system. 

In the examples below, replace ALCFUserID with your ALCF user id.
```bash
ssh ALCFUserID@sambanova.alcf.anl.gov
Password: < MobilePASS+ code >
```

Note: Use the ssh "-v" option in order to debug any ssh problems.

Once you are on the login node, ssh to one of the sambanova compute node.
```bash
ssh sn30-r1-h1       
```

It is also recommended to ssh to other compute nodes namely, `sn30-r1-h1`, `sn30-r1-h2`, `sn30-r2-h1`, `sn30-r2-h2`, `sn30-r3-h1`, `sn30-r3-h2`, `sn30-r4-h1`, `sn30-r4-h2`. Note: This avoids all your jobs being queued up on the same node.  

## Job Queuing and Submission

SambaNova uses Slurm for job submission and queueing. Below are some of the important commands for using Slurm.

* `srun` : can be used to run individual Python scripts in parallel with other scripts on a cluster managed by Slurm.
* `sbatch` :jobs can be submitted to the Slurm workload manager through a batch script by using the sbatch command.
* `squeue` : command provides information about jobs located in the Slurm scheduling queue.
* `sinfo` : is used to view partition and node information for a system running Slurm.
* `scancel` : is used to signal or cancel jobs, job arrays, or job steps.

## Hands-on Example

* [GPT-2](./gpt.md)

## Additonal Examples

* [BERT](./bert/bert.md)

##### Copy Applications to `$HOME` directory

Sambanova software stack and associated environmental variables are automatically setup at login for a SN30 node. 

Each of the samples or application examples provided by SambaNova has its own pre-built virtual environment which can be readily used. They are present in the `/opt/sambaflow/apps/` directory tree within each of the applications. This directory contains all the different models currently supported with the Sambanova software stack.

<!---
Copy them to your `$HOME` directory
```bash
cp -r /opt/sambaflow/apps/ ~
```
--->

## Homework

For [BERT example](./bert/), understand flags used in the script. Change values for flag `--ntasks` and measure its effect on performance. Submit proof (contents printed out to your terminal, path to a logfile or screenshot) that you were able to successfully follow the instructions and execute.

### Homework response

After [fixing a syntax error in BertLarge_run.sh](https://github.com/tstabrawa/ai-science-training-series/commit/60cbe5d533e939efb611740d9f1154e686e3ea38), I ran BertLarge.sh with `--ntasks 16` (the default), `--ntasks 32`, and `--ntasks 8`.  For `--ntasks 16` I received the error: `sbatch: error: Batch job submission failed: Requested node configuration is not available`, prompting me to try `--ntasks 8`.  I didn't notice any significant difference in the performance (run-time) between `--ntasks 16` and `--ntasks 8`.  (Both completed at iteration #504 after 7-8 minutes with similar values for `final_loss` -- though slightly higher than for `--ntasks 16`.)  I also tried running with `--ntasks 8 --ntasks-per-node 8`, but I also saw no significant (run-time) performance difference (though the `final_loss` was also higher than for `--ntasks 16`).  If the higher loss after 505 iterations is actually consistently higher, this suggests that training to the same loss value with `--ntasks 8` would take longer than training with `--ntasks 16`.

#### Detailed results:

##### --ntasks 16: (--ntasks-per-node 16)

```
Iteration:  20%|█▉        | 504/2551 [07:46<30:00,  1.14it/s]2024-11-21 22:29:02,177 - apps.tasks.lm_tasks.bert_mlperf_trainer - Rank 0, PID 1890343 - info     - epoch:0|local_step:505|global_step:505|average_loss:8.20269|step_loss:8.20269|step_ns_loss:0.59640|step_mlm_loss:7.60629|learning_rate:7.06e-06|eval_step:0.00000|validation_average_loss:0.00000|validation_total_loss:0.00000|validation_mlm_loss:0.00000|validation_ns_loss:0.00000
2024-11-21 22:29:02,185 - apps.tasks.lm_tasks.bert_mlperf_trainer - Rank 0, PID 1890343 - info     - final_loss 8.254242
2024-11-21 22:29:02,198 - apps.tasks.lm_tasks.bert_mlperf_trainer - Rank 0, PID 1890343 - info     - {'e2e_train_time': 470.3965759277344, 'training_sequences_per_second': 562855.7977443167, 'final_loss': 8.254241943359375}
```

##### --ntasks 32: (--ntasks-per-node 16)

```
sbatch: error: Batch job submission failed: Requested node configuration is not available
```

##### --ntasks 8: (--ntasks-per-node 16)

```
Iteration:  20%|█▉        | 504/2549 [07:53<29:42,  1.15it/s]2024-11-21 23:14:54,597 - apps.tasks.lm_tasks.bert_mlperf_trainer - Rank 0, PID 3045226 - info     - epoch:0|local_step:505|global_step:505|average_loss:8.23332|step_loss:8.23332|step_ns_loss:0.60576|step_mlm_loss:7.62756|learning_rate:7.06e-06|eval_step:0.00000|validation_average_loss:0.00000|validation_total_loss:0.00000|validation_mlm_loss:0.00000|validation_ns_loss:0.00000
2024-11-21 23:14:54,605 - apps.tasks.lm_tasks.bert_mlperf_trainer - Rank 0, PID 3045226 - info     - final_loss 8.308189
2024-11-21 23:14:54,613 - apps.tasks.lm_tasks.bert_mlperf_trainer - Rank 0, PID 3045226 - info     - {'e2e_train_time': 479.74279952049255, 'training_sequences_per_second': 275945.1942422435, 'final_loss': 8.308189392089844}
```

##### --ntasks 8 --ntasks-per-node 8:

```
Iteration:  20%|█▉        | 504/2549 [07:31<29:44,  1.15it/s]2024-11-21 23:36:59,494 - apps.tasks.lm_tasks.bert_mlperf_trainer - Rank 0, PID 3660377 - info     - epoch:0|local_step:505|global_step:505|average_loss:8.23336|step_loss:8.23336|step_ns_loss:0.60619|step_mlm_loss:7.62718|learning_rate:7.06e-06|eval_step:0.00000|validation_average_loss:0.00000|validation_total_loss:0.00000|validation_mlm_loss:0.00000|validation_ns_loss:0.00000
2024-11-21 23:36:59,503 - apps.tasks.lm_tasks.bert_mlperf_trainer - Rank 0, PID 3660377 - info     - final_loss 8.308196
2024-11-21 23:36:59,514 - apps.tasks.lm_tasks.bert_mlperf_trainer - Rank 0, PID 3660377 - info     - {'e2e_train_time': 455.1042366027832, 'training_sequences_per_second': 290884.3938439188, 'final_loss': 8.308196067810059}
```

#### Relevant log files

* [HW7-sambanova-shell-session.txt](HW7-results/HW7-sambanova-shell-session.txt)
* [HW7-BertLarge-16tasks-1stlog.out](HW7-results/HW7-BertLarge-16tasks-1stlog.out)
* [HW7-BertLarge-16tasks-2ndlog.out](HW7-results/HW7-BertLarge-16tasks-2ndlog.out)
* [HW7-BertLarge-32tasks-1stlog-failed.out](HW7-results/HW7-BertLarge-32tasks-1stlog-failed.out)
* [HW7-BertLarge-8tasks-1stlog.out](HW7-results/HW7-BertLarge-8tasks-1stlog.out)
* [HW7-BertLarge-8tasks-2ndlog.out](HW7-results/HW7-BertLarge-8tasks-2ndlog.out)
* [HW7-BertLarge-8tasks-8pernode-1stlog.out](HW7-results/HW7-BertLarge-8tasks-8pernode-1stlog.out)
* [HW7-BertLarge-8tasks-8pernode-2ndlog.out](HW7-results/HW7-BertLarge-8tasks-8pernode-2ndlog.out)
* [slurm-43715.out](HW7-results/slurm-43715.out)
* [slurm-43716.out](HW7-results/slurm-43716.out)
* [slurm-43717.out](HW7-results/slurm-43717.out)
* [slurm-43720.out](HW7-results/slurm-43720.out)
* [slurm-43721.out](HW7-results/slurm-43721.out)

## Additional Resources

* [ALCF Sambanova Documentation](https://docs.alcf.anl.gov/ai-testbed/sambanova/getting-started/)
* [Sambanova Documentation](https://docs.sambanova.ai/developer/latest/sambaflow-intro.html) 
* Sambanova applications path: `/opt/sambaflow/apps/`
* Sambanova model scripts: `/data/ANL/scripts/`
* Important datasets: `/software/sambanova/dataset/`
