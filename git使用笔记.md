* 初始化Git仓库使用`git init`
* 添加文件到Git仓库，分两步：
  1. 使用命令`git add <file>`,注意，可反复多次使用，添加多个文件；
  2. 使用命令`git commit -m <message>`,完成。
* 要随时掌握工作区的状态，使用`git status`命令。
* 如果`git staus`告诉你有文件修改过，用`git diff`可以查看修改内容。
* `HEAD`指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`.
* 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。
* 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。
    >$ git reset --hard HEAD^ 返回上一个版本 ^个数表示回到多少版本之前，也可以换成如HEAD~100    

    >$ git reset --hard 1094a   commit_id不用写全，但是要写到git可以找到唯一的版本，不确定commit_id可以通过`git log` `git reflog`查看commit_id
* 每次修改，如果不用`git add`到暂存区，那就不会加入到`commit`中。
* a.乱改了工作区某个文件内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。
* b.当乱改了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`,就回到了场景a,第二步按a操作。
* c.当已经提交了不合适的修改到版本库时，想要撤销本次提交，可以使用版本回退命令`git reset --hard commit_id`，前提是没有推送到远程库
* 在文件管理器中把没用的文件删了，或者用`rm <file>`命令删了，Git知道删除了文件，工作区和版本库不一样了，`git status`命令会告诉你哪些文件被删除了，现在两种情况，确实要从版本库中删除该文件，就用命令`git rm <file>`删掉，并且`git commit`。
    >命令`git rm <file>`用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。
* 要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；
* 关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；
* 此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改；
* 分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步。
* 要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。
* Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。
* Git鼓励大量使用分支：
  查看分支：`git branch`
  创建分支：`git branch <name>`
  切换分支：`git checkout <name>`
  创建+切换分支：`git checkout -b <name>`
  合并某分支到当前分支：`git merge <name>`
  删除分支：`git branch -d <name>`
* 实战分支系列：
  首先创建`dev`分支，然后切换到`dev`分支：
  >$ git checkout -b dev
  Switch to a new branch 'dev' 

  `git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：
  ```
    $ git branch dev
    $ git checkout dev
  ```
  然后用`git branch`命令来查看当前分支,当前分支前面会标`*`号:
  ```
    $ git branch
    * dev
      master
  ```
  然后就可以在`dev`分支上正常提交，`git add` `git commit`
  现在，`dev`分支的工作完成可以切回`master`分支:
  >$ git checkout master
  Switched to branch 'master'

  然后，把`dev`分支的工作成果合并到`master`分支上：
  >$ git merge dev 
  ……

  `git merge`命令用语合并指定分支到当前分支。合并后可以看到和`dev`分支的最新提交是完全一样的。
  合并语句后的`fast-forword`信息，表示的是本次合并是“`快进模式`”，也就是直接把`master`指向`dev`的当前提交，合并速度很快，当然还有其他方式合并。
  合并完成后就可以放心删除`dev`分支：
  >$ git branch -d dev
  ……
  
  因为创建、合并和删除分支非常快，所以Git鼓励使用分支完成某个任务，合并后再删掉分支，这和直接在`master`分支上工作效果是一样的，但过程更安全。
* 合并分支往往也不是一帆风顺的。合并冲突解决方案：
  ([教程-解决冲突](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001375840202368c74be33fbd884e71b570f2cc3c0d1dcf000))
* 当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。
  解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。
  用`git log --graph`命令可以看到分支合并图。
* 通常，合并分支时，如果可能，Git会用`fast-forword`模式，但这种模式下删除分支后，会丢掉分支信息。
  如果强制禁用`fast-forword`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
* 准备合并dev分支，注意`--no-ff`参数，表示禁用`fast-forword`：
  >$ git merge --no-ff -m "<版本修改提示>" dev

  因为本次合并要创建一个新的commit，所以要加上`-m`参数，把commit描述进去，合并后用`git log`查看分支历史
  >$ git log --graph --pretty=oneline --abbrev-commit
  ……
* **分支策略**
  在实际开发中，我们应该按照几个基本原则进行分支管理：
  首先，`master`分支应该非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
  干活都在`dev`分支上，也就是说`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；
  所有人都在`dev`分支上干活，每个人都有自己的分支，是不是地往`dev`分支上合并就可以了。
 ![分支策略图](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384909239390d355eb07d9d64305b6322aaf4edac1e3000/0)
* 合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast-forword合并就看不出来曾经做过合并
* 修复bug时，通过创建新的bug分支进行修复，然后合并，最后删除；
  Git还提供了一个`stash`功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作;当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，在`git stash pop`，回到工作现场。
  * Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；另一种方式是用`git stash pop`，恢复的同时把stash内容也删了。再用`git stash list`查看，就看不到任何stash内容了。
  可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：
    >$ git stash apply stash@{0}
* 开发一个新feature，最好新建一个分支；如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。
  `$ git branch -d <name> 删除`
  `git branch -D <name> 强行删除`
* 多人写作的工作模式通常是这样：
  >1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
  >2. 如果推送失败，则是因为远程分支比你的本地更新，需要先用`git pull`试图合并；
  >3. 如果合并有冲突，则解决冲突，并在本地提交；
  >4. 没有冲突或者解决冲突后，再用`git push origin <branch-name>`推送就能成功！

  如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。
* 查看远程库信息，使用`git remote -v` ；
  本地新建的分支如果不推送到远程，对其他人就是不可见的；
  从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
  从本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
  建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
  从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。
* `git rebase`操作可以把本地未push的分叉提交历史整理成直线；
  `git rebase`的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。
  `git rebase`优点是把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了。
* `git tag <tagname>`用于新建一个标签，默认为HEAD，也可以指定一个commit_id；
  `git tag -a <tagname> -m "……" `可以指定标签信息；
  `git tag -a <tagname> -m "……" commit_id`创建带有说明的标签，用-a指定标签名，-m指定说明文字;
  `git show <tagname>`查看标签信息;
  `git tag`可以查看所有标签。
  >注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。
* 命令`git push origin <tagname>`可以推送一个本地标签；
  命令`git push origin --tags`可以推送全部未推送过的本地标签；
  命令`git tag -d <tagname>`可以删除一个本地标签；
  命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。