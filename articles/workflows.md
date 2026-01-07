https://github.com/avelino/awesome-go?tab=readme-ov-file#workflow-frameworks

# Workflow automation domain knowledge

## BPMN 2.0

https://www.bpmn.org/



# Cadence (8.9k stars)

## What I don't like

- Too verbose framework
- I'm executing everything via wrapper functions, I'd prefer more explicit framework:

```go
go func() {
    
}()
```

- Too many (27) tables in the DB
- Encoded `data` columns with the content like `0x06000A00000A000C00000000000000000A000E00000000000000000A00101866C365C9F65A9900` (thriftrw). I believe I don't need binary encoding by default.
- Bloated context of activities.

https://github.com/cadence-workflow/cadence

## Questions

- When a signal happens how workflow receives this signal and restores, by cron, `LISTEN`?
- How async activities work?
- How to signal?
- How to make changes in workflow logic without loosing actively executed workflows?
- What's the best practice to prototype workflows fast?
  - How to handle errors?

# MyWorkflow

Requirements:

- potentially long lived activity (heartbeating);
- async (waiting for the reply of email);
- retries according to a specific retry policy;
- dispatching through queues;
- rate and parallelism limits per queue/worker;
- workflows react to external events;
- workflows orchestrate activities;
- signals to running workflows;
- execution of workflow based on cron;
- easy workflow logic changes;
- easy monitoring+logging of workflow executions;
- easy testing;
- record warnings related to each workflow (let's say I have to parse JSON and it doesn't have desirable field but this lacking field should not stop workflow execution)

# Based on my live project (SS)

Arch solution: several cron jobs.

## Disadvantages of my solution

- there is no one source of truth about the workflow: I can't observe whole logic of my workflow. I'd like it to be like:
```go
func workflow(ctx context.Context, dep1 *Dep1, dep2 *Dep2) error {
    user := ctx.GetUser()
    err := dep1.Step1(user)
    if err != nil {
        return utils.WrapErr(err)
    }
    err := dep2.Step2(user)
    if err != nil {
        return utils.WrapErr(err)
    } 
    return nil
}
```

# Links

- https://www.youtube.com/watch?v=llmsBGKOuWI - good demonstration of evolution with queues
