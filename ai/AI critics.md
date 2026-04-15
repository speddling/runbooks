
Our team uses a set of \"critics\" (agents with different personalities
and playbooks) with specialized context. So there\'s one for style,
security, testing, etc. I think we have 9 or so. There\'s a main agent
that does orchestration, then a bunch of sub agents are spawned to look
at the code and fix it up, etc....

Hey everyone and thanks for the invite!The idea behind AI critics is
this:With the LLMs of today, we are fundamentally constrained by the
attention mechanism. There have been great improvements over the past
year or so---like paged attention, flash attention, etc.---but at the
end of the day, when we use a single coding agent, it inevitably loses
track of the peripheral things we care about (like code performance,
strict coding standards, best practices, and testing behavior rather
than just wiring). It drops these constraints because its attention is
mostly consumed by getting the primary job done (building the feature or
fixing the bug).So, one solution is to split up that attention by
creating AI subagents that focus on exactly one thing each. By having
these \"AI critics\" (each enforcing a specific rule in the repo), the
system as a whole performs better, catches subtle bugs, and strictly
enforces best practices.This is similar to the reason why Cursor\'s bug
bot or Claude\'s code review always finds issues with the code that the
main agent originally wrote. Because when the AI is running its initial
generation loop, its context and \"cognitive load\" are maxed out on
simply solving the immediate problem. It doesn\'t have the attention
span left to self-critique. By separating the \"Creator\" from the
\"Critics,\" we give each agent a single, focused objective, resulting
in much higher quality, production-ready code.That is the experiment
we\'re running. We\'ve built it in a way that if there are gaps in the
system, we just keep adding new rules. It turns enforcing best practices
into a continuously evolving, shared responsibility for the entire team.


I see some mention of git hooks\... is everything local, or do you have
automation for PR reviews as well?

it\'s local and opt in for now,\
it can be automated, we talked about potentially adding it to claude
code hooks so after work is done it spawns the subagents to do the job
automatically,\
it could very well be added as a task in CI as well\
i\'m thinking if it runs locally, we can have quicker cycle time
(instead of waiting for ci to run, can cycle caster locally where
development happens)

Can we talk practically about how you accomplish this?  Is it something
like defining N critic agents (perf, security, etc) in markdown, then
running each manually?  Or do you have another agent whose instructions
are "start these 5 sub agents". I\'m still trying to wrap my head around
the whole multi agent workflow.

Or are you doing this through dozens of smaller rules? 

in this system i just prompt:\
*can you run critics?*\
and it spawns subagents to do the work\
for systems that havent set it up, they can run something like\
*for each md file in ai-rules, spawn a subagent to analyze our code
change with the rules set in the md file. reply with fail or pass, if
there are issues to address reference the path-to-file-name:line*\
with the results returned from the critics, the main agent that
originally did the work can reflect and fix\

<https://github.com/thegoodparty/ai-rules> 

This with the \"competition\" would be interesting to try

<div>

</div>
