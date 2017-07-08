Git is a version control system.
Git is free software.

steps:
	01 选择一个合适的地方，创建一个空目录：
		$ mkdir learngit
		pwd命令用于显示当前目录。

	02 通过git init命令把这个目录变成Git可以管理的仓库：
		$ git init

	03 一定要放到learngit目录下（子目录也行），
	    因为这是一个Git仓库，放到其他地方Git再厉害也找不到这个文件。
			用命令git add告诉Git，把文件添加到仓库：
				$ git add readme.txt
			用命令git commit告诉Git，把文件提交到仓库：
				$ git commit -m "wrote a readme file"
				-m后面输入的是本次提交的说明，可以输入任意内容，
				当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。
			为什么Git添加文件需要add，commit一共两步呢？因为commit可以一次提交很多文件，
			所以你可以多次add不同的文件，比如：
				$ git add file1.txt
				$ git add file2.txt file3.txt
				$ git commit -m "add 3 files."

	04 运行git status命令看看结果：
		$ git status
	   看具体修改了什么内容
		$ git diff readme.txt

	05 版本控制系统肯定有某个命令可以告诉我们历史记录，
		在Git中，我们用git log命令查看：
		$ git log
		如果嫌输出信息太多，可以加上--pretty=oneline参数：
		$ git log --pretty=oneline
		Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本
		上一个版本就是HEAD^，上上一个版本就是HEAD^^
		当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。
		$ git reset --hard HEAD^
		HEAD^处  填写 commit id  用log/reflog查看
		当你用$ git reset --hard HEAD^回退到add distributed版本时，
		再想恢复到append GPL，就必须找到append GPL的commit id。
		Git提供了一个命令git reflog用来记录你的每一次命令：
		$ git reflog

	06 工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。
		Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，
		还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。
		第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区；
		第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。
		add 是 把工作区的更新到暂存区，commit是把缓冲区更新到仓库。
		所以经过add, commit,修改再add,再修改，就会出现工作区、缓冲区、和仓库三者都不同。
		就可以下面的比较了：
		git diff 是工作区和 中间区比较，git diff --cached是中间区和仓库比较。

	07  为什么Git比其他版本控制系统设计得优秀，因为Git跟踪并管理的是修改，而非文件。
		第一次修改 -> git add -> 第二次修改 -> git commit
		你看，我们前面讲了，Git管理的是修改，当你用git add命令后，在工作区的第一次修改被放入暂存区，准备提交，
		但是，在工作区的第二次修改并没有放入暂存区，
		所以，git commit只负责把暂存区的修改提交了，
		也就是第一次的修改被提交了，第二次的修改不会被提交。

	08  git add files 把当前文件放入暂存区域。
		git commit 给暂存区域生成快照并提交。
		git reset -- files 用来撤销最后一次git add files，你也可以用git reset 撤销所有暂存区域文件。
		git checkout -- files 把文件从暂存区域复制到工作目录，用来丢弃本地修改。
		Git会告诉你，git checkout -- file可以丢弃工作区的修改：
		$ git checkout -- readme.txt
		命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：
		一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
		一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
		总之，就是让这个文件回到最近一次git commit或git add时的状态。

		用命令git reset HEAD file可以把暂存区的修改撤销掉（unstage），

		场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
		场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，
		第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。
		场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。


	09  从历史的提交建立新分支的方法：
		方法一： checkout到历史提交，然后checkout -b。
		方法二： reset到历史提交，checkout -b，然后再reset到原来的版本。
		方法三： git branch <branch> <start point>

		将某个历史版本全部拉到工作区和暂存区：
		方法一： 可能的需求是为了将过去删除掉的修改重新应用到最新的版本，这时可以先回到历史版本处建立分支，然后回到原来的最新的版本，进行merge分支的操作。
		方法二： reset加上hard参数到需要的历史版本，然后再reset加上soft参数回来。

		将历史版本的某文件版本拉到当前工作区或者暂存区进行处理：
		方法一： git reset HEAD~2 foo.py，直接拉到暂存区。
		方法二： git checkout HEAD~2 foo.py，拉到工作区和暂存区。

		已经有添加到暂存区的文件修改，之后又进行了修改。想要都撤销掉，变为和仓库中的版本相同（仓库覆盖工作和暂存）：
		方法一：1、git reset HEAD file 清空暂存区的提交，变为和仓库中的版本相同。2、git checkout  --  file 以暂存区为蓝本，覆盖掉工作区。
		方法二：git checkout HEAD --  file 。

		已经添加到暂存区的修改之后又进行了修改，想要都撤销掉，变为和仓库中的版本相同（仓库覆盖工作和暂存）：
		方法一：git reset --hard HEAD 重设HEAD，hard参数覆盖工作区和暂存区。
		方法二：强制切换到其他分支丢弃更改，然后再切回来。

		撤销当前工作区的文件修改，变为和暂存区相同（暂存覆盖工作）：
		方法一：git checkout -- file 暂存区覆盖工作区(以暂存区为蓝本，覆盖掉工作区)。

		撤销添加到暂存区的文件修改，将修改退回到工作区（暂存先覆盖工作，然后仓库覆盖暂存）：
		方法一：1、git checkout  --  file 以暂存区为蓝本，覆盖掉工作区。 2、git reset HEAD file 清空暂存区的提交，变为和仓库中的版本相同。

		清空暂存区文件修改：
		方法一：git reset -- file 清空暂存区的文件修改。

		清空暂存区：
		方法一：git reset HEAD file 清空暂存区。

		checkout文件层面的操作：
		主要对暂存区和工作区起作用，一般有暂存区覆盖工作区的行为特征。
		reset文件层面的操作：
		主要对暂存区起作用。

	10 你通常直接在文件管理器中把没用的文件删了，或者用rm命令删了：
		$ rm test.txt
		现在你有两个选择，
		一是确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit：
		另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：
		$ git checkout -- test.txt

	11 远程



