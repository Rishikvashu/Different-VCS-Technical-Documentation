# Version Control Systems: Complete Technical Documentation

## Concepts, Types, Tools, Workflows, and Migration

---

<a id="table-of-contents"></a>

# Table of Contents

1. [Introduction to Version Control](#s1)
2. [Why Do We Need Version Control?](#s2)
3. [Types of Version Control Systems](#s3)
4. [Core Concepts and Terminology](#s4)
5. [Git](#s5)
6. [Git Hosting Platforms (GitHub, GitLab, Bitbucket, etc.)](#s6)
7. [Subversion (SVN)](#s7)
8. [Mercurial](#s8)
9. [Perforce Helix Core](#s9)
10. [Legacy Systems: CVS and TFVC](#s10)
11. [Other Version Control Systems](#s11)
12. [Complete VCS Comparison](#s12)
13. [Branching Strategies](#s13)
14. [Merge vs Rebase](#s14)
15. [Large Files and Monorepos](#s15)
16. [Security in Version Control](#s16)
17. [Version Control in CI/CD](#s17)
18. [Migration Between Version Control Systems and Platforms](#s18)
19. [Choosing the Right VCS](#s19)
20. [Commands Cheat Sheet](#s20)
21. [Best Practices](#s21)
22. [Final Checklist](#s22)
23. [Glossary](#s23)

---

<a id="s1"></a>

# 1. Introduction to Version Control

## 1.1 Version Control kya hota hai?

Version Control System (VCS) ek aisa system hai jo files aur code mein hone wale changes ko time ke saath track karta hai.

Isse ye pata chalta hai ki:

* Kisne change kiya.
* Kab change kiya.
* Kya change kiya.
* Kyun change kiya (commit message ke through).

Agar koi change galat ho jaye, to VCS ki help se pichle working version par wapas jaya ja sakta hai.

### Example

Maan lo tumhari team ek Python microservice par kaam kar rahi hai:

`attendance-api`

* Developer A `app.py` mein attendance logic change karta hai.
* Developer B same project mein `Dockerfile` update karta hai.
* Developer C naya feature branch par bana raha hai.

VCS ke bina teen log ek hi folder ko zip karke share karenge, aur changes overwrite hone ka risk rahega.

VCS ke saath har developer apni changes alag track kar sakta hai aur baad mein unhe safely merge kar sakta hai.

**Important:** VCS sirf code ke liye nahi hota. Documentation, configuration files, infrastructure code (Terraform, Ansible), aur kabhi-kabhi design assets bhi version control mein rakhe jaate hain.

## 1.2 VCS, Git aur GitHub mein confusion

Ye teen cheezein alag hain:

| Term | Kya hai? | Example |
| ---- | -------- | ------- |
| Version Control System | Changes track karne ka concept/tool | Git, SVN, Mercurial |
| Git | Ek specific distributed VCS tool | `git commit` |
| GitHub / GitLab | Git repositories host karne wale platforms | github.com, gitlab.com |

Git ek tool hai, GitHub ek service hai jo Git repositories host karti hai aur uske upar extra features deti hai (pull requests, issues, CI, etc.).

[⬆ Back to ToC](#table-of-contents)

---

<a id="s2"></a>

# 2. Why Do We Need Version Control?

## 2.1 History aur Traceability

Har change ka record rehta hai.

Agar production mein bug aata hai, to commit history dekhkar identify kiya ja sakta hai ki bug kis change ke saath aaya.

```bash
git log --oneline
git blame app.py
```

## 2.2 Collaboration

Multiple developers ek hi codebase par parallel kaam kar sakte hain.

Branches ki help se har developer apna kaam isolate rakh sakta hai.

## 2.3 Rollback

Agar naya release problem kare, to pichle stable version par wapas jaya ja sakta hai.

```bash
git revert <commit-id>
```

## 2.4 Backup aur Disaster Recovery

Distributed VCS mein har clone repository ki full history rakhta hai (Git ke case mein), isliye ek server fail hone par bhi data recover ho sakta hai.

Lekin ye proper backup strategy ka replacement nahi hai. Hosting platform aur server ka alag backup bhi plan karna chahiye.

## 2.5 Code Review aur Quality

Pull Request ya Merge Request workflow mein changes merge hone se pehle review ho sakte hain aur CI checks run ho sakte hain.

## 2.6 Release Management

Tags ki help se specific commit ko release version ke roop mein mark kiya ja sakta hai.

```bash
git tag -a v1.0.0 -m "First stable release"
```

[⬆ Back to ToC](#table-of-contents)

---

<a id="s3"></a>

# 3. Types of Version Control Systems

VCS ko architecture ke basis par teen categories mein divide kiya ja sakta hai.

## 3.1 Local Version Control

Is model mein versions sirf ek single computer par local database mein store hote hain.

Example tools:

* RCS (Revision Control System)

Limitations:

* Collaboration mushkil hai.
* Machine fail hone par history lost ho sakti hai.

## 3.2 Centralized Version Control (CVCS)

Ek central server hota hai jahan repository ki main history store hoti hai. Developers server se files checkout karte hain aur changes wapas server par commit karte hain.

```text
Developer A ----\
Developer B -----+----> Central Server (Repository)
Developer C ----/
```

Example tools:

* Subversion (SVN)
* Perforce Helix Core
* CVS
* TFVC (Team Foundation Version Control)

Advantages:

* Simple mental model.
* Central access control aur auditing.
* Large binary files aur large repositories ke liye kuch tools (jaise Perforce) optimized hote hain.

Limitations:

* Server down ho to commit/history operations affected ho sakte hain.
* Offline kaam limited hota hai.
* Branching aur merging historically zyada heavy ho sakti hai (tool par depend karta hai).

## 3.3 Distributed Version Control (DVCS)

Har developer ke paas repository ki poori copy (history ke saath) hoti hai.

```text
Developer A (full repo) <----> Remote Server <----> Developer B (full repo)
```

Example tools:

* Git
* Mercurial
* Fossil
* Pijul
* Sapling, Jujutsu (Git-compatible)

Advantages:

* Offline commit, branch aur history inspection possible hai.
* Fast local operations.
* Multiple remotes aur flexible workflows.
* Har clone ek backup copy ki tarah kaam kar sakta hai.

Limitations:

* Concepts (staging, rebase, remote tracking) seekhne mein time lagta hai.
* Bahut badi history ya binary files ke saath clone slow ho sakta hai (partial clone, LFS jaise solutions available hain).

## 3.4 Comparison Table

| Feature | Local | Centralized | Distributed |
| ------- | ----- | ----------- | ----------- |
| Full history local machine par | Haan | Generally nahi | Haan |
| Offline commit | Haan | Limited | Haan |
| Central server zaroori | Nahi | Haan | Optional (convention se hota hai) |
| Collaboration | Weak | Strong | Strong |
| Example | RCS | SVN, Perforce | Git, Mercurial |

[⬆ Back to ToC](#table-of-contents)

---

<a id="s4"></a>

# 4. Core Concepts and Terminology

Ye concepts almost har VCS mein milte hain, naam alag ho sakte hain.

| Concept | Meaning |
| ------- | ------- |
| Repository (Repo) | Project files aur unki poori history ka container |
| Working directory / Working copy | Wo folder jahan tum actual files edit karte ho |
| Commit / Revision / Changeset | Changes ka ek recorded snapshot ya set |
| Branch | Development ki alag line, jisse parallel kaam hota hai |
| Merge | Do branches ke changes ko combine karna |
| Tag | Kisi specific commit ko naam dena (jaise release version) |
| Clone | Remote repository ki local copy banana |
| Remote | Dusra repository jo network par available hai |
| Push | Local commits ko remote par bhejna |
| Pull / Fetch | Remote se changes lana (`pull` = fetch + merge/rebase in Git) |
| Checkout / Switch | Kisi branch ya revision par jana |
| Conflict | Jab do changes same jagah par overlap karein aur automatic merge na ho paye |
| Diff | Do versions ke beech ka difference |
| HEAD | Git mein current checked-out commit ka pointer |
| Staging area (Index) | Git mein wo area jahan commit se pehle changes select karke rakhe jaate hain |

## 4.1 Basic Workflow (Git style)

```text
Working Directory --(git add)--> Staging Area --(git commit)--> Local Repository --(git push)--> Remote Repository
```

## 4.2 Conflict ka example

Maan lo do developers ne same line alag tarike se change ki:

```text
<<<<<<< HEAD
timeout = 30
=======
timeout = 60
>>>>>>> feature/timeout-change
```

Developer ko manually decide karna hota hai ki kaunsi value rakhni hai, phir file save karke commit karna hota hai.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s5"></a>

# 5. Git

## 5.1 Git kya hai?

Git ek free aur open-source distributed version control system hai. Ise Linus Torvalds ne 2005 mein Linux kernel development ke liye banaya tha.

Aaj Git duniya ka sabse zyada use hone wala VCS hai.

## 5.2 Git kaise kaam karta hai?

Git data ko snapshots ki tarah store karta hai. Har commit project ki us waqt ki state ka reference hota hai.

Git objects content-addressable hote hain, yaani har object ka ek hash ID hota hai.

Main object types:

* **Blob:** file ka content
* **Tree:** directory structure
* **Commit:** tree, parent commit, author aur message ka record
* **Tag:** kisi commit ka annotated naam

## 5.3 Basic Setup

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Config check karna:

```bash
git config --list
```

## 5.4 Daily Workflow

Repository banana ya clone karna:

```bash
git init
git clone https://github.com/org/attendance-api.git
```

Status dekhna:

```bash
git status
```

Changes stage karna aur commit karna:

```bash
git add app.py
git commit -m "Add attendance calculation"
```

Remote par push karna:

```bash
git push origin main
```

Remote se updates lena:

```bash
git fetch origin
git pull origin main
```

## 5.5 Branching in Git

Naya branch banana aur us par jana:

```bash
git switch -c feature/attendance-calculation
```

Purane tarike se:

```bash
git checkout -b feature/attendance-calculation
```

Branch list:

```bash
git branch
git branch -a
```

Branch merge karna:

```bash
git switch main
git merge feature/attendance-calculation
```

Branch delete karna:

```bash
git branch -d feature/attendance-calculation
```

## 5.6 Undo aur Recovery Commands

| Situation | Command |
| --------- | ------- |
| Working file ke unstaged changes discard karna | `git restore <file>` |
| Staged file ko unstage karna | `git restore --staged <file>` |
| Last commit ka message badalna (abhi push nahi hua) | `git commit --amend` |
| Kisi commit ko safely undo karna (naya commit banta hai) | `git revert <commit-id>` |
| Branch pointer ko pichle commit par le jana | `git reset <commit-id>` |
| Temporary changes side par rakhna | `git stash` |
| Stash wapas lana | `git stash pop` |
| Kisi ek commit ko dusre branch par lana | `git cherry-pick <commit-id>` |
| Lost commits dhundna | `git reflog` |

**Important:** `git reset --hard` working directory ke uncommitted changes delete kar deta hai. Ise soch samajhkar use karo.

Shared branch (jaise `main`) par history rewrite karna (`reset`, `rebase`, force push) generally avoid karna chahiye.

## 5.7 Git History Dekhna

```bash
git log --oneline
git log --oneline --graph --all
git show <commit-id>
git blame app.py
git diff main..feature/attendance-calculation
```

## 5.8 Git ke Advantages aur Limitations

### Advantages

* Fast local operations.
* Powerful branching aur merging.
* Bahut bada ecosystem (GitHub, GitLab, IDE integrations, CI tools).
* Offline kaam.
* Industry standard, isliye hiring aur learning resources zyada.

### Limitations

* Learning curve (index, rebase, detached HEAD, etc.).
* Large binary files ke liye extra tooling (Git LFS) chahiye.
* Bahut bade monorepos mein performance tuning ya partial clone/sparse checkout jaisi features ki zaroorat ho sakti hai.
* Fine-grained per-directory access control Git mein built-in nahi hota; ye usually hosting platform ya workflow se handle hota hai.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s6"></a>

# 6. Git Hosting Platforms (GitHub, GitLab, Bitbucket, etc.)

## 6.1 Platform aur VCS ka difference

Git sirf version control tool hai. Hosting platforms Git repositories ko server par host karte hain aur collaboration features add karte hain.

```text
Git (tool)  +  Hosting Platform  =  Pull Requests, Issues, CI/CD, Access Control, Code Review
```

## 6.2 Common Platforms

| Platform | Type | Notable Features |
| -------- | ---- | ---------------- |
| GitHub | Cloud (aur Enterprise Server self-hosted option) | Pull Requests, GitHub Actions, Issues, Projects, Code Owners, Packages |
| GitLab | Cloud aur self-managed | Merge Requests, built-in CI/CD (`.gitlab-ci.yml`), Issues, Container Registry |
| Bitbucket | Cloud aur Data Center | Pull Requests, Jira integration, Pipelines |
| Azure Repos (Azure DevOps) | Cloud aur Server | Git repositories, Boards, Pipelines, (TFVC support bhi) |
| Gitea / Forgejo | Self-hosted, lightweight | Open-source, simple setup, Git hosting aur basic collaboration |
| AWS CodeCommit | Cloud | AWS ecosystem integration. Availability aur status ke liye official AWS documentation check karo. |

**Note:** Platforms ke features aur plans time ke saath change hote rehte hain. Current details ke liye official documentation dekhna best hai.

## 6.3 Pull Request vs Merge Request

| Platform | Term |
| -------- | ---- |
| GitHub, Bitbucket, Azure Repos | Pull Request (PR) |
| GitLab | Merge Request (MR) |

Dono ka concept same hai: ek branch ke changes ko dusre branch mein merge karne ka request, jisme review aur CI checks hote hain.

## 6.4 Typical PR Workflow

```text
Create Branch
     |
     v
Commit Changes
     |
     v
Push Branch
     |
     v
Open Pull Request
     |
     v
CI Checks + Code Review
     |
     v
Approval
     |
     v
Merge
```

## 6.5 Merge Options (platform par)

| Option | Kya hota hai? |
| ------ | ------------- |
| Merge commit | Dono branches ki history preserve hoti hai, ek merge commit banta hai |
| Squash and merge | Branch ke saare commits ek single commit mein combine ho jaate hain |
| Rebase and merge | Commits target branch ke upar replay hote hain, merge commit nahi banta |

Team ko ek convention decide karni chahiye taaki history consistent rahe.

## 6.6 Platform-level Controls

* **Branch protection:** `main` branch par direct push rokna, review aur CI checks mandatory karna.
* **Code owners:** Kisi folder/file ke liye required reviewers define karna (`CODEOWNERS` file).
* **Access control:** Read, Write, Admin jaise roles.
* **Webhooks:** Push ya PR events par Jenkins jaise CI tool ko trigger karna.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s7"></a>

# 7. Subversion (SVN)

## 7.1 SVN kya hai?

Subversion (SVN) Apache Software Foundation ka open-source centralized version control system hai. Ye CVS ke successor ke roop mein banaya gaya tha.

## 7.2 SVN ki khaas baatein

* **Centralized model:** Ek central repository hoti hai.
* **Atomic commits:** Ek commit ya to poora apply hota hai ya bilkul nahi.
* **Global revision numbers:** Poori repository ka ek incrementing revision number hota hai (r1, r2, r3...).
* **Directory versioning:** Directories aur renames bhi track hote hain.
* **Path-based authorization:** Repository ke alag paths par alag permissions set kiye ja sakte hain.
* **Partial checkout:** Poori repo ki jagah sirf ek subdirectory checkout kar sakte ho.

## 7.3 Standard Repository Layout

```text
repo/
├── trunk/        # Main development line
├── branches/     # Feature/release branches
└── tags/         # Release snapshots
```

SVN mein branch aur tag asal mein directory copies hote hain (`svn copy`), jo cheap hoti hain kyunki SVN internally storage efficiently handle karta hai.

## 7.4 Basic Commands

Checkout:

```bash
svn checkout https://svn.example.com/repo/trunk attendance-api
```

Update (server se latest lana):

```bash
svn update
```

File add karna:

```bash
svn add newfile.py
```

Status aur diff:

```bash
svn status
svn diff
```

Commit:

```bash
svn commit -m "Add attendance calculation"
```

History:

```bash
svn log
```

Branch banana:

```bash
svn copy ^/trunk ^/branches/feature-x -m "Create feature-x branch"
```

Branch par switch karna:

```bash
svn switch ^/branches/feature-x
```

Merge:

```bash
svn merge ^/branches/feature-x
```

## 7.5 SVN vs Git (short)

| Point | SVN | Git |
| ----- | --- | --- |
| Model | Centralized | Distributed |
| Offline commit | Nahi | Haan |
| Revision ID | Sequential number | Hash |
| Branching | Directory copy | Lightweight pointer |
| Partial checkout | Easy | Sparse checkout se possible |
| Path-based permissions | Built-in | Platform/workflow par depend |

## 7.6 SVN kab use hota hai?

* Purane projects jinki history SVN mein hai.
* Jahan path-level access control aur central governance chahiye.
* Teams jo simple centralized workflow prefer karti hain.

Naye projects mein zyadatar teams Git choose karti hain.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s8"></a>

# 8. Mercurial

## 8.1 Mercurial kya hai?

Mercurial ek distributed version control system hai jo Python mein likha gaya hai. Ye Git ke saath hi 2005 mein launch hua tha.

Mercurial ka focus simple aur consistent command-line experience par raha hai.

## 8.2 Key Features

* Distributed model (Git ki tarah full local history).
* Simple command set aur consistent behavior.
* Extensions ke through functionality badhai ja sakti hai.
* Staging area nahi hota (by default changes directly commit hote hain).

## 8.3 Basic Commands

```bash
hg init
hg clone https://hg.example.com/repo
hg status
hg add file.py
hg commit -m "Add feature"
hg push
hg pull
hg update
hg log
```

Branch aur bookmark:

```bash
hg branch feature-x
hg bookmark feature-y
```

Merge:

```bash
hg merge
hg commit -m "Merge"
```

## 8.4 Mercurial vs Git

| Point | Mercurial | Git |
| ----- | --------- | --- |
| Staging area | Nahi | Haan |
| Command consistency | Zyada consistent maana jaata hai | Zyada powerful, par commands kaafi hain |
| History rewriting | Extensions ke through, default mein restricted | Built-in (`rebase`, `amend`) |
| Hosting ecosystem | Limited | Bahut bada |

## 8.5 Current Status

Mercurial ab bhi use hota hai, lekin hosting aur tooling ecosystem Git ke muqable chhota hai. Kai major platforms ne Mercurial support kam ya band kar diya hai (jaise Bitbucket Cloud ne Mercurial support hata diya tha). Mercurial use karne se pehle hosting options verify kar lo.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s9"></a>

# 9. Perforce Helix Core

## 9.1 Perforce kya hai?

Perforce Helix Core (jise P4 bhi kaha jata hai) ek commercial version control system hai jo bahut bade repositories aur large binary files ke liye jaana jaata hai.

Isse game development, hardware design, media aur enterprise projects mein use kiya jaata hai jahan assets bade hote hain.

## 9.2 Key Concepts

| Term | Meaning |
| ---- | ------- |
| Depot | Server par repository ka top-level container |
| Workspace (Client) | Developer ki local mapping jo depot ke kuch hisse ko local disk se jodti hai |
| Changelist | Ek logical group of file changes jo saath submit hoti hain |
| Submit | Changelist ko server par commit karna |
| Shelve | Changes ko submit kiye bina server par temporarily save karna |
| Stream | Structured branching model jo parent-child relationship define karta hai |
| File locking | Exclusive checkout, mainly un files ke liye jinhe merge nahi kiya ja sakta (binary assets) |

## 9.3 Basic Commands

```bash
p4 sync
p4 edit file.cpp
p4 add newfile.cpp
p4 opened
p4 submit
p4 changes
p4 shelve
```

## 9.4 Strengths

* Bahut bade repositories aur large binary files ko efficiently handle karta hai.
* Exclusive file locking (unmergeable binary files ke liye useful).
* Fine-grained permissions.
* Teams jo sirf ek subset of repository sync karna chahti hain unke liye accha.

## 9.5 Limitations

* Commercial licensing (chhoti teams ke liye free tier ho sakta hai, current terms official site par dekho).
* Git ke muqable community aur third-party tooling kam.
* Ek centralized server par dependency.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s10"></a>

# 10. Legacy Systems: CVS and TFVC

## 10.1 CVS (Concurrent Versions System)

CVS ek purana centralized version control system hai, jo SVN se pehle bahut popular tha.

### Known limitations

* Commits atomic nahi hote (ek commit beech mein fail ho sakta hai aur partial state bach sakti hai).
* Directory renames properly track nahi hote.
* Branching aur merging limited aur painful hoti hai.
* Active development ab bahut kam hai.

### Basic commands

```bash
cvs checkout project
cvs update
cvs commit -m "message"
```

Naye projects ke liye CVS recommended nahi hai. Agar purani CVS repository hai to migrate karna reasonable option hai.

## 10.2 TFVC (Team Foundation Version Control)

TFVC Microsoft ka centralized version control system hai jo Team Foundation Server aur Azure DevOps ka hissa raha hai.

Azure DevOps mein do repository types mile hain:

* Git
* TFVC

Microsoft naye projects ke liye Git recommend karta hai, aur TFVC mainly existing projects mein milta hai. Current support status ke liye official Azure DevOps documentation check karo.

### TFVC ke concepts

* Changeset: TFVC ka commit jaisa concept.
* Check-in / Check-out: Files edit karne ke liye lock-based ya server workspace model.
* Server workspace aur Local workspace.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s11"></a>

# 11. Other Version Control Systems

## 11.1 Fossil

Fossil SQLite ke creator D. Richard Hipp ne banaya hai.

* Distributed VCS.
* Single self-contained executable.
* Repository ek single file mein store hoti hai.
* Built-in bug tracker, wiki aur web interface.

```bash
fossil init repo.fossil
fossil open repo.fossil
fossil add file.txt
fossil commit -m "Initial commit"
fossil ui
```

Small teams aur self-contained projects ke liye useful hai.

## 11.2 Pijul

Pijul ek distributed VCS hai jo patch-based theory par based hai.

* Changes ko patches ki tarah treat karta hai.
* Merge ka behavior patches ke independence par based hota hai.
* Ecosystem aur adoption abhi limited hai.

## 11.3 Sapling

Sapling Meta (Facebook) ka source control system hai.

* Large monorepos ke liye design kiya gaya hai.
* Git-compatible hai (Sapling client Git repositories ke saath kaam kar sakta hai).
* Stacked commits aur easier history editing par focus.

## 11.4 Jujutsu (jj)

Jujutsu ek Git-compatible version control system hai.

* Git repository ko backend ki tarah use kar sakta hai, isliye existing Git hosting (GitHub, GitLab) ke saath kaam kar sakta hai.
* Working copy automatically commit ki tarah track hoti hai, isliye staging area ka concept nahi hota.
* Operation log ke through undo/redo support.

Naya tool hai aur teams ko adopt karne se pehle apni workflow compatibility test kar leni chahiye.

## 11.5 Bazaar (Breezy)

Bazaar ek purana DVCS tha (Canonical ne develop kiya tha). Active development kam ho gayi, aur Breezy usse fork hokar continue hua. Naye projects mein generally use nahi hota.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s12"></a>

# 12. Complete VCS Comparison

| System | Model | Offline Work | Branching | Large Binary Files | Typical Use | Ecosystem |
| ------ | ----- | ------------ | --------- | ------------------ | ----------- | --------- |
| Git | Distributed | Haan | Lightweight, powerful | Git LFS se | General software development, open source | Bahut bada |
| SVN | Centralized | Limited | Directory copy based | Achha handle karta hai | Legacy projects, central governance | Medium |
| Mercurial | Distributed | Haan | Branches, bookmarks | Moderate | Teams jo simple CLI prefer karti hain | Chhota |
| Perforce Helix Core | Centralized (Streams ke saath) | Limited | Streams | Bahut achha | Games, media, large enterprise assets | Medium, commercial |
| CVS | Centralized | Nahi | Limited | Weak | Legacy only | Bahut kam |
| TFVC | Centralized | Limited (local workspace) | Branch/merge, heavy | Moderate | Existing Azure DevOps/TFS projects | Microsoft ecosystem |
| Fossil | Distributed | Haan | Haan | Moderate | Small self-contained projects | Chhota |
| Pijul | Distributed (patch-based) | Haan | Patch based | Limited | Experimental, research | Bahut chhota |
| Sapling | Distributed (Git-compatible) | Haan | Stacked commits | Large repos ke liye designed | Monorepo-style workflows | Chhota par growing |
| Jujutsu | Distributed (Git-compatible) | Haan | Haan | Git ke jaisa | Git users jo alag workflow chahte hain | Chhota par growing |

**Note:** Ye table general guidance hai. Actual capabilities version, configuration aur hosting setup par depend karti hain.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s13"></a>

# 13. Branching Strategies

Branching strategy decide karti hai ki team kaise branches banayegi, kaise merge karegi aur kaise release karegi.

## 13.1 Git Flow

Branches:

* `main`: Production-ready code
* `develop`: Integration branch
* `feature/*`: Naye features
* `release/*`: Release preparation
* `hotfix/*`: Production urgent fixes

```text
main     ----------------o-----------o--------
                          \         / 
develop  ---o------o-------o-------o----------
             \    /         \     /
feature       o--o           o---o
```

### Kab useful hai?

* Scheduled releases wale projects.
* Multiple versions ko parallel support karna ho.

### Limitation

* Overhead zyada hai, aur long-lived branches se merge conflicts badh sakte hain.

## 13.2 GitHub Flow

Simple workflow:

1. `main` hamesha deployable rakho.
2. Naya kaam ke liye branch banao.
3. Commits karo aur push karo.
4. Pull Request kholo.
5. Review aur CI checks ke baad merge karo.
6. Merge ke baad deploy karo.

### Kab useful hai?

* Continuous delivery wali web applications aur microservices.

## 13.3 Trunk-Based Development

Sab developers `main` (trunk) par chhote aur frequent changes integrate karte hain. Branches bahut short-lived hoti hain.

Incomplete features ko feature flags ke peeche rakha ja sakta hai.

### Kab useful hai?

* Mature CI/CD aur strong automated testing wali teams.

## 13.4 Strategy Comparison

| Point | Git Flow | GitHub Flow | Trunk-Based |
| ----- | -------- | ----------- | ----------- |
| Complexity | High | Low | Low |
| Branch lifetime | Long | Short | Bahut short |
| Release cadence | Scheduled | Frequent | Very frequent |
| Automated testing dependency | Medium | High | Bahut high |
| Best for | Versioned releases | Web apps, microservices | High-velocity CI/CD teams |

## 13.5 Branch Naming Convention (example)

```text
feature/attendance-calculation
bugfix/login-timeout
hotfix/payment-crash
release/1.2.0
```

Team ko ek consistent naming convention follow karni chahiye.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s14"></a>

# 14. Merge vs Rebase

## 14.1 Merge

Merge do branches ki histories ko combine karta hai aur (agar fast-forward possible nahi hai to) ek merge commit banata hai.

```bash
git switch main
git merge feature/x
```

### Types of merge

| Type | Description |
| ---- | ----------- |
| Fast-forward | Target branch mein koi naya commit nahi tha, isliye pointer simply aage badh jata hai |
| Three-way merge | Dono branches mein alag changes hain, isliye merge commit banta hai |
| Squash merge | Saare commits ek commit mein combine hokar apply hote hain |

### Advantages

* Original history preserve hoti hai.
* Shared branches par safer hai.

### Limitation

* History mein merge commits badh sakte hain.

## 14.2 Rebase

Rebase tumhare branch ke commits ko kisi dusre base commit ke upar replay karta hai.

```bash
git switch feature/x
git rebase main
```

### Advantages

* Linear, clean history.

### Limitation

* Commits ke hash change ho jaate hain (history rewrite hoti hai).
* Jo branch dusre log use kar rahe hain, us par rebase karna problem create kar sakta hai.

## 14.3 Golden Rule

Jo commits already shared/pushed hain aur jin par dusre log kaam kar rahe hain, unhe rebase ya force-push se rewrite mat karo.

## 14.4 Quick Comparison

| Point | Merge | Rebase |
| ----- | ----- | ------ |
| History | Non-linear, accurate record | Linear |
| Rewrites history | Nahi | Haan |
| Safe on shared branches | Haan | Nahi (generally avoid) |
| Conflict resolution | Ek baar | Commit by commit ho sakta hai |

[⬆ Back to ToC](#table-of-contents)

---

<a id="s15"></a>

# 15. Large Files and Monorepos

## 15.1 Large Files ki problem

Git poori history store karta hai. Agar large binary file (video, dataset, build artifact) commit ho jaye, to wo history mein hamesha rahti hai aur repository size badh jaati hai.

## 15.2 Git LFS (Large File Storage)

Git LFS large files ko repository ke bahar alag storage par rakhta hai aur repository mein sirf pointer file store karta hai.

```bash
git lfs install
git lfs track "*.psd"
git add .gitattributes
```

Hosting platforms par LFS storage aur bandwidth ke limits ya charges ho sakte hain. Current limits official documentation mein check karo.

## 15.3 Git ke Performance Features

| Feature | Purpose |
| ------- | ------- |
| Shallow clone (`--depth`) | Sirf recent history clone karna |
| Partial clone (`--filter`) | Kuch objects (jaise large blobs) on-demand download karna |
| Sparse checkout | Repository ke sirf selected directories working directory mein rakhna |

Example:

```bash
git clone --depth 1 https://github.com/org/repo.git
git clone --filter=blob:none https://github.com/org/repo.git
```

CI pipelines mein shallow clone build time kam karne mein help kar sakta hai, lekin kuch tools (jaise version calculation ya blame-based analysis) ko full history chahiye hoti hai.

## 15.4 Monorepo vs Multi-repo

| Point | Monorepo | Multi-repo |
| ----- | -------- | ---------- |
| Structure | Ek repository mein multiple projects/services | Har project ki alag repository |
| Code sharing | Easy | Packages/libraries ke through |
| Atomic cross-project change | Easy | Mushkil |
| Access control | Complex ho sakta hai | Repo level par simple |
| CI complexity | Changed paths detect karne ki zaroorat | Simple per repo |
| Tooling requirement | Strong tooling chahiye | Kam |

Bahut bade monorepos ke liye Perforce, Sapling ya specialized Git tooling use hoti hai.

## 15.5 Large files ko history se hatana

Agar galti se large file commit ho gayi hai, to `git filter-repo` ya BFG Repo-Cleaner jaise tools se history rewrite ki ja sakti hai.

Ye shared repository par disruptive operation hai, kyunki sabke clones ko dobara sync karna padta hai. Pehle team se coordinate karo.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s16"></a>

# 16. Security in Version Control

## 16.1 Secrets Commit mat karo

Passwords, API keys, tokens aur private keys repository mein commit nahi karne chahiye.

Agar secret commit ho gaya hai:

1. Us credential ko turant **revoke ya rotate** karo.
2. Phir zaroorat ho to history se remove karo.

Sirf latest commit se line delete karna sufficient nahi hai, kyunki secret history mein bacha rehta hai.

Prevention ke liye pre-commit hooks aur CI mein secret scanning (jaise Gitleaks) use ki ja sakti hai.

## 16.2 `.gitignore`

Aisi files jinhe track nahi karna chahiye unhe `.gitignore` mein daalo.

```text
.env
*.pem
__pycache__/
node_modules/
dist/
```

`.gitignore` sirf untracked files par kaam karta hai. Agar file pehle se tracked hai, to use `git rm --cached <file>` se untrack karna padega.

## 16.3 Signed Commits

Commit signing se verify kiya ja sakta hai ki commit genuinely us author ne kiya hai.

Git GPG, SSH keys ya S/MIME ke through signing support karta hai.

```bash
git config --global commit.gpgsign true
git commit -S -m "Signed commit"
```

Hosting platforms signed commits ko "Verified" label ke saath dikha sakte hain.

## 16.4 Branch Protection

Important branches (`main`, `release/*`) par protection rules lagao:

* Direct push block karna.
* Minimum review approvals required karna.
* CI checks pass hone par hi merge allow karna.
* Force push aur branch deletion rokna.

## 16.5 Access Control

* Least privilege principle follow karo: sirf utni access do jitni zaroori hai.
* Personal access tokens aur SSH keys ko regularly review aur rotate karo.
* Multi-factor authentication (MFA) enable karo.
* Ex-employees ya inactive users ki access hatao.

## 16.6 Git Hooks

Hooks aise scripts hain jo Git events par automatically chalte hain.

| Hook | Kab chalta hai? | Use |
| ---- | --------------- | --- |
| `pre-commit` | Commit se pehle | Lint, secret scan, format check |
| `commit-msg` | Commit message likhne ke baad | Message format validate karna |
| `pre-push` | Push se pehle | Tests chalana |

Local hooks developer bypass kar sakta hai (`--no-verify`), isliye important checks server-side ya CI mein bhi enforce karo.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s17"></a>

# 17. Version Control in CI/CD

## 17.1 Integration ka basic flow

```text
Developer
   |
   v
git push / Pull Request
   |
   v
Hosting Platform (GitHub / GitLab / etc.)
   |
   v  (Webhook)
CI Server (Jenkins / GitHub Actions / GitLab CI)
   |
   v
Checkout -> Build -> Test -> Scan -> Artifact
```

## 17.2 Triggers

| Trigger | Description |
| ------- | ----------- |
| Webhook | Repository event (push, PR) par platform CI ko HTTP notification bhejta hai |
| Polling | CI server periodically repository check karta hai |
| Scheduled | Fixed time par build chalta hai |
| Manual | User khud pipeline start karta hai |

Webhook polling se zyada efficient hota hai kyunki build tabhi chalta hai jab change aata hai.

## 17.3 Jenkins mein checkout ka example

```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/org/attendance-api.git',
                    credentialsId: 'github-credentials'
            }
        }
    }
}
```

Credentials ko Jenkins Credentials store mein rakho, Jenkinsfile mein hardcode mat karo.

## 17.4 Status Checks

CI result ko Pull Request par status check ke roop mein report kiya ja sakta hai. Branch protection ke saath ye ensure karta hai ki failing checks wala code merge na ho.

## 17.5 GitOps (short)

GitOps mein Git repository ko infrastructure aur deployment configuration ka source of truth maana jata hai. Repository mein change merge hone par automation environment ko desired state par le aati hai.

Example: Kubernetes manifests Git mein rakhna aur Argo CD ya Flux se sync karna.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s18"></a>

# 18. Migration Between Version Control Systems and Platforms

Migration do tarah ki ho sakti hai:

1. **VCS migration:** ek VCS se doosre VCS mein (jaise SVN se Git).
2. **Platform migration:** same VCS (Git) rakhkar ek hosting platform se doosre par (jaise GitLab se GitHub).

## 18.1 Migration se pehle planning

* Repositories ki inventory banao (size, branches, tags, LFS usage).
* Decide karo kitni history migrate karni hai.
* Users, teams aur permissions ka mapping plan karo.
* CI/CD pipelines, webhooks aur integrations ko identify karo jo dobara configure karne padenge.
* Pehle ek chhoti test repository par dry run karo.
* Migration window ke dauran developers ko push karne se roko (freeze).
* Rollback plan rakho aur original repository ko archive/read-only rakho.

## 18.2 SVN se Git

```bash
git svn clone https://svn.example.com/repo --stdlayout --no-metadata attendance-api
```

`--stdlayout` tab use karo jab repository mein `trunk/branches/tags` standard layout ho.

Migration ke baad:

* SVN branches aur tags ko Git branches/tags mein convert karna.
* Author names ko mapping file (`--authors-file`) se Git identities par map karna.

## 18.3 Mercurial se Git

`hg-fast-export` ya `git-remote-hg` jaise tools use kiye jaate hain.

Migration ke baad history, branches aur tags verify kar lo.

## 18.4 Perforce se Git

`git p4` Git ke saath aata hai aur Perforce depot se history import kar sakta hai.

```bash
git p4 clone //depot/project@all
```

Bahut bade depots ya streams ke liye extra planning aur tools (jaise specialized migration services) chahiye ho sakte hain.

## 18.5 Platform Migration: Git repository ek jagah se doosri jagah

Basic repository (code, branches, tags) move karne ke liye mirror method:

```bash
git clone --mirror https://gitlab.com/org/repo.git
cd repo.git
git push --mirror https://github.com/org/repo.git
```

Ye method sirf Git data (commits, branches, tags) move karta hai.

### Ye move nahi hota

* Issues, Pull/Merge Requests, comments
* Wiki (alag repository hoti hai, alag move karni padti hai)
* CI/CD configuration ko nayi platform ke format mein convert karna padta hai (jaise `.gitlab-ci.yml` se GitHub Actions workflows)
* Webhooks, secrets, variables
* Users aur permissions

Issues aur PR/MR metadata move karne ke liye platform-specific importers ya migration tools use hote hain, jaise GitHub ka importer aur GitHub Enterprise Importer (`gh gei`). Current capabilities ke liye official documentation check karo.

## 18.6 Bulk Migration (multiple repositories/accounts)

Jab bahut saari repositories migrate karni ho:

* Repositories ki list CSV ya script se tayyar karo.
* Migration tool ka bulk mode ya API automation use karo.
* Batches mein migrate karo aur har batch ke baad validate karo.
* Rate limits aur API quotas ka dhyan rakho.
* Migration logs save karo taaki failed repositories dobara try ki ja sakein.

## 18.7 Post-Migration Validation Checklist

* [ ] Branch count aur tag count match karte hain.
* [ ] Latest commit hash source aur target par same hai.
* [ ] Repository size reasonable hai.
* [ ] LFS objects migrate hue hain.
* [ ] Issues aur PR/MR (agar migrate kiye gaye) sahi dikh rahe hain.
* [ ] Team aur user permissions set hain.
* [ ] CI/CD pipelines nayi platform par chal rahi hain.
* [ ] Webhooks aur integrations update hue hain.
* [ ] Old repository read-only ya archived hai.
* [ ] Team ko nayi remote URL ki information di gayi hai.

Developers ko remote URL update karna hota hai:

```bash
git remote set-url origin https://github.com/org/repo.git
git remote -v
```

[⬆ Back to ToC](#table-of-contents)

---

<a id="s19"></a>

# 19. Choosing the Right VCS

## 19.1 Decision Guide

| Situation | Recommended Choice | Reason |
| --------- | ------------------ | ------ |
| Naya software project, general team | Git | Industry standard, bada ecosystem |
| Open-source project | Git (GitHub/GitLab) | Contributors ki familiarity aur tooling |
| Game development, bade binary assets | Perforce Helix Core (ya Git + LFS) | Large file handling aur file locking |
| Purani SVN repository, stable workflow | SVN rakho ya Git par migrate karo | Migration cost vs benefit evaluate karo |
| Chhota self-contained project jisme issue tracker/wiki bhi chahiye | Fossil | All-in-one single binary |
| Bahut bada monorepo | Sapling, Perforce ya optimized Git setup | Scale handling |
| Existing Azure DevOps TFVC project | Git par migrate karna consider karo | Git ka bada ecosystem aur Microsoft ka recommendation |

## 19.2 Evaluation Criteria

Decision lene se pehle in questions par socho:

* Team size kitni hai aur kitni distributed hai?
* Repository size aur binary files kitni hain?
* Offline kaam ki zaroorat hai?
* Path-level access control chahiye?
* Existing tooling (CI, IDE, code review) kis VCS ko support karta hai?
* Team ki skills kya hain?
* Hosting: cloud ya self-hosted?
* Compliance aur audit requirements kya hain?

## 19.3 Practical Recommendation

Zyadatar naye software projects ke liye Git sabse practical starting point hai kyunki:

* Developers usse generally already jaante hain.
* CI/CD, code review aur DevOps tools ka support sabse zyada hai.
* Hosting options bahut hain (cloud aur self-hosted).

Specialized needs (bahut bade binary assets, strict locking) ho to hi alternatives evaluate karo.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s20"></a>

# 20. Commands Cheat Sheet

## 20.1 Common Operations: Git vs SVN vs Mercurial

| Operation | Git | SVN | Mercurial |
| --------- | --- | --- | --------- |
| Repository banana | `git init` | `svnadmin create repo` | `hg init` |
| Copy lena | `git clone <url>` | `svn checkout <url>` | `hg clone <url>` |
| Status | `git status` | `svn status` | `hg status` |
| File add | `git add <file>` | `svn add <file>` | `hg add <file>` |
| Commit | `git commit -m "msg"` | `svn commit -m "msg"` | `hg commit -m "msg"` |
| Server par bhejna | `git push` | (commit hi server par jata hai) | `hg push` |
| Server se lena | `git pull` | `svn update` | `hg pull -u` |
| History | `git log` | `svn log` | `hg log` |
| Diff | `git diff` | `svn diff` | `hg diff` |
| Branch banana | `git switch -c <name>` | `svn copy ^/trunk ^/branches/<name>` | `hg branch <name>` |
| Merge | `git merge <branch>` | `svn merge <url>` | `hg merge` |
| Tag | `git tag <name>` | `svn copy ^/trunk ^/tags/<name>` | `hg tag <name>` |

## 20.2 Git: Most Used Commands

```bash
# Setup
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Start
git init
git clone <url>

# Daily
git status
git add <file>
git commit -m "message"
git push
git pull

# Branches
git branch
git switch -c <branch>
git switch <branch>
git merge <branch>
git branch -d <branch>

# History
git log --oneline --graph --all
git show <commit>
git blame <file>

# Undo
git restore <file>
git restore --staged <file>
git revert <commit>
git stash
git stash pop

# Remote
git remote -v
git remote add origin <url>
git fetch --all --prune

# Tags
git tag -a v1.0.0 -m "Release 1.0.0"
git push origin v1.0.0
```

## 20.3 Good Commit Message ka format

```text
Short summary (50 characters ke around)

Optional detailed explanation: kya change kiya aur kyun.
Related issue ya ticket reference.
```

Example:

```text
Fix attendance calculation for overnight shifts

Previously end time earlier than start time produced a negative value.
Now the calculation adds 24 hours when the shift crosses midnight.

Refs: SCRUM-123
```

[⬆ Back to ToC](#table-of-contents)

---

<a id="s21"></a>

# 21. Best Practices

## 21.1 Chhote aur Focused Commits Karo

Ek commit mein ek logical change rakho. Isse review, revert aur debugging aasan hoti hai.

## 21.2 Meaningful Commit Messages Likho

"fix" ya "update" jaise vague messages ki jagah batao kya aur kyun change hua.

## 21.3 Main Branch ko Stable Rakho

`main` branch hamesha working state mein honi chahiye. Naya kaam feature branches par karo.

## 21.4 Pull Request Chhote Rakho

Bade PRs review karna mushkil hota hai. Chhote PRs jaldi review hote hain aur conflicts kam hote hain.

## 21.5 Frequently Sync Karo

Long-lived branch ko regularly `main` ke saath update karte raho taaki conflicts bade na hon.

## 21.6 CI Checks Mandatory Karo

Branch protection ke saath CI checks (tests, lint, secret scan) merge se pehle required karo.

## 21.7 Generated aur Sensitive Files Commit Mat Karo

Build outputs, dependencies folders (`node_modules`), `.env` files aur credentials `.gitignore` mein rakho.

## 21.8 Shared History Rewrite Mat Karo

Pushed aur shared branches par force push avoid karo. Zaroorat ho to team ko inform karo.

## 21.9 Tags se Releases Mark Karo

Release versions ke liye semantic versioning (`v1.2.3`) ke saath tags use karo.

## 21.10 Branches Clean Rakho

Merge ho chuki branches delete karo taaki repository organized rahe.

## 21.11 README aur Contribution Guide Rakho

Repository mein README, branching convention, commit message format aur contribution guidelines document karo.

## 21.12 Backup aur Access Review

Hosting platform ka backup strategy aur access permissions periodically review karo.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s22"></a>

# 22. Final Checklist

### Repository Setup

* [ ] Repository ka clear naam aur README hai.
* [ ] `.gitignore` configured hai.
* [ ] License file hai (agar zaroorat ho).
* [ ] Default branch (`main`) defined hai.

### Workflow

* [ ] Branching strategy decide aur document ki gayi hai.
* [ ] Branch naming convention defined hai.
* [ ] Commit message format define hai.
* [ ] Merge strategy (merge/squash/rebase) team ne decide ki hai.

### Protection and Review

* [ ] `main` par branch protection enabled hai.
* [ ] Minimum reviewer approvals required hain.
* [ ] CI status checks mandatory hain.
* [ ] Code owners define hain (jahan applicable ho).

### Security

* [ ] Secrets repository mein commit nahi hain.
* [ ] Secret scanning configured hai.
* [ ] MFA enabled hai.
* [ ] Access least privilege par based hai.
* [ ] Signed commits (agar policy require kare) configured hain.

### CI/CD Integration

* [ ] Webhook ya trigger configured hai.
* [ ] CI credentials secure store mein hain.
* [ ] Pipeline status PR par dikhta hai.

### Large Files

* [ ] Large binary files ke liye LFS ya alternative plan hai.
* [ ] Repository size monitor hoti hai.

### Migration (agar applicable ho)

* [ ] Inventory aur plan ready hai.
* [ ] Test migration successful hui.
* [ ] Post-migration validation complete hai.
* [ ] Old repository archive ki gayi hai.
* [ ] Team ko updated remote URLs bataye gaye hain.

[⬆ Back to ToC](#table-of-contents)

---

<a id="s23"></a>

# 23. Glossary

| Term | Meaning |
| ---- | ------- |
| VCS | Version Control System |
| DVCS | Distributed Version Control System |
| CVCS | Centralized Version Control System |
| Repository | Project files aur history ka storage |
| Commit | Changes ka recorded snapshot/set |
| Branch | Development ki alag line |
| Merge | Do branches ke changes combine karna |
| Rebase | Commits ko kisi dusre base ke upar replay karna |
| Tag | Specific commit ka naam (jaise release) |
| Clone | Remote repository ki full local copy |
| Fork | Kisi repository ki apne account mein copy (hosting platform concept) |
| Remote | Network par available repository |
| Origin | Default remote ka common naam |
| HEAD | Current checked-out commit ka pointer (Git) |
| Index / Staging Area | Commit se pehle changes ka selection area (Git) |
| Working Directory | Local folder jahan files edit hoti hain |
| Pull Request / Merge Request | Branch merge karne ka review request |
| Conflict | Overlapping changes jo automatically merge nahi hote |
| Fast-forward | Merge jisme sirf branch pointer aage badhta hai |
| Squash | Multiple commits ko ek commit mein combine karna |
| Cherry-pick | Ek specific commit ko dusri branch par apply karna |
| Stash | Uncommitted changes ko temporarily side par rakhna |
| Reflog | Git ka log jo HEAD ke movements record karta hai |
| Hook | Git event par automatically chalne wala script |
| Webhook | Event par HTTP notification bhejne wala mechanism |
| Git LFS | Large files ko manage karne ka Git extension |
| Monorepo | Ek hi repository mein multiple projects |
| Trunk | Main development line (jaise `main`) |
| Changelist | Perforce mein file changes ka logical group |
| Depot | Perforce ka repository container |
| Revision | VCS mein ek version ya commit ka reference |
| GitOps | Git ko infrastructure aur deployment ka source of truth banana |
| Signed Commit | Cryptographic signature wala commit |
| Branch Protection | Important branches par rules lagana (review, CI, no force push) |

[⬆ Back to ToC](#table-of-contents)

---

<a id="conclusion"></a>

# Conclusion

Version control modern software development ki foundation hai. Ye sirf code save karne ka tarika nahi, balki collaboration, traceability, review, security aur release management ka core mechanism hai.

Zyadatar naye projects ke liye **Git** practical default hai, aur **GitHub, GitLab, Bitbucket** jaise platforms uske upar collaboration aur CI/CD features dete hain. **SVN, Perforce, Mercurial** aur dusre systems specific situations mein ab bhi useful hain, khaaskar legacy projects, bade binary assets ya special governance requirements mein.

Practical starting setup:

* **Git:** Version control
* **GitHub / GitLab:** Hosting aur code review
* **Branch protection:** Main branch ki safety
* **CI (Jenkins / GitHub Actions / GitLab CI):** Automated checks
* **Secret scanning:** Credentials leak se bachav
* **Git LFS:** Large files ke liye, zaroorat ho to

Sabse important principle ye hai:

**Tool se zyada important hai ek clear workflow: chhote commits, reviewed merges, protected main branch, aur har change ka traceable history.**

[⬆ Back to ToC](#table-of-contents)
