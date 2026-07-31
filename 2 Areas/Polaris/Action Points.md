# Points
- Share knowledge base
	- Obsidian Vault => Git => Implemented => Integrated
- The PRD need a decline and the iterative process to distill the PRD. Not Yet
- Building => Polaris ? HOW => Question for Mirco => Fri

02/07/2026
- A more editable comment on the PRD for refine the PRD 
- Sync all change in interrogation Room
- Future edition of GG Cloud web app + Centralize the generated content + DB
-  Share knowledge base
	- Each person a folder (unique)
	- Check persona folder first
	- Merge to share folder based on what logic
		- 
	- The chat structure:
		- Chat
		- Content
		- Misc
- We agree to:
	- Save MD (or any kind of file) of AI-Generated to the REPO itself.
	- 
Meeting Questions:
- Why we don't have markdown (or any kind of file) to summarize the code => Ask Mirco (team MUJI have this feature and get rejected )
- We demand the Polaris to keep an eye on the file in the REPO (Granify tool). Do it (on our tool) locally until Polaris's feature is released?


03/07/2026
- Action:
	- Get know about the auto crrate skill feature => Lean about this feature
	- Implement the feedback about the each step and each process flow
![[Pasted image 20260703155914.png|697]]
	- Each tab for the feedbacks with the skill set of "interrogate-*" + the skill from Polaris
	- Process Flow exporter => MD file (or any file LLM can read to implement)
- Questions:
	- Do we deploy the Vinnstack to Cloud
	- If Yes:
		- Login: Google Auth
			- Credentials Onboarding Wizard
		- Graphify JSON
			- Centralized (technical account with READ ONLY on all repos)
			- CRON to refresh JSON every X hours
		- Context/Chat/Obsidian
			- Stored per account
		- Interrogation Room
			- Centralize storage (Questions & Answers, PRD, Stories)
	- If  No: 
		- Integrate Vinnstack to Polaris
	UI/UX
	![[Pasted image 20260703134830.png]] Need to be a tab + can comment to re-generate.

06/07/2026
In Business phase add a save button to save every thing + trigger sync to vault


===============================================================
Polaris:
1. Knowledge-base per account: NO
2. 

Clear onboarding:
1. On cloud, Claude Code Subscription (ultracode descope) NOT WORK, Vertex AI DO WORK
	1. My Skills is account-base => Save to the DB or Cloud Storage per account
	2. VinnStack + Agent Skills is centralized
	3. TOKEN ARE ON LOCAL ONLY.
2. Graphify: 
	1. Service account with read only for all repo => use when Vinnstack clone or pull so that create the graph (result as JSON file) and store to centralize (no account - No mater who have the same view).
	2. Vault from chat (account based) to cloud storage.

08/07/2026
- Update: Do not create ticket on Jira when approve PRD, Do push after approve the Process Flow (Story).
- In mermaid diagram, the text is being cut off, make sure it show completely 
- New: Separate into smaller Stories (Process Flow). How to Split? => By add instruction in the prompt (free text)
- New: Add delete button to remove Process Flow
- ===========================================================



21/07/2026
- Compare 2 approach:
- 1. Existing skill
- 2. Adapts skill : take current skill + take the problem "This is too details, we need a more high level design"
	- In our pipeline we have an epic from JIRA which we will break down in multiple rounds. First round is the business interrogation, which uses the "interrogate-business" skill. This one is fine. After the first round we take the results and apply the "interrogate-technical" skill. This one is problematic. Based on the Developers the questions are too detailed, making it hard to actually answer them. We need the interrogate-technical skill to be adapted so it returns more high level questions/suggested answers. the content of epic: C:\Users\dvtliem\Kepler\vinnstack\doc\jira-LUZ-156281.md . The previous generated business interrogate-business and interrogate-technical: C:\Users\dvtliem\Kepler\vinnstack\doc\interrogation-LUZ-156281.md
No — they operate at different levels:

- **`approvePrdToJira`** (PRD → Stories): the **initial** decomposition. Input is the whole approved PRD; output is the _first_ set of Stories. This is where stories come into existence.
- **`splitStory`** (Story → Stories): a **refinement** of one _already-created_ Story. Input is a single Story + your free-text instruction (e.g. "split by user role"); output is 2+ smaller slices of just that one.

Same skill (`prd-to-story` knows how to cut work into vertical slices), but different **contract + input**:

- approvePrdToJira → `REST_SPEC_CONTRACT`, fed the PRD.
- splitStory → `SPLIT_CONTRACT`, fed one Story.

So: approve makes the stories; split re-slices one of them when it turned out too big. You need both — approve can't know in advance which story you'll later decide to break down.


Max 30 questions (done).
Merge Thanh Skill to story-to-process-flow (half-done).

22/7/2026


TODO:
Make the tech interrogate comment and regen.
Change "Runs the `epic-to-prd` skill via Claude over the committed answers — this takes ~30–90s." => "Runs the `epic-to-prd` skill via Claude over the committed answers — this takes up to {match-timeout} minutes" - DONE
- Onboard: ask user to choose the path for folder (create if not exist) - In TEST
Important:
1. prd-to-story: need the v2. - IN TEST
2. Merge the Story to Process Flow to prd-to-story so that the breakdown is included in the story creation
3. While "## Architecture & Flow" or relevant diagram not show image yet keep a spinner. - DONE
4. Merge Thanh skill to the prd-to-story with horizontal style. - IN TEST
Next step:
5. Schedule mode for the generate (Claude Routine) => Thinking about 24/24 - TODO
Plan:
6. On friday, send email for skills to contrbute
7. Ask Fuji if they need a meeting.

23/7
- AFK considered to be feasible in next major version 3.0.0
- After contribute the skill to Polaris , sync the skill to local folder to use

24/7
- How we deal with TECHNICAL-ERROR. I suggest to use a general technical error message.