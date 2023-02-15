## Rerere
#git 

Rerere stands for *reuse recorded resolution*. This tool records how Git resolved a previous hunk conflict and resolves any subsequent conflicts of the same nature based on the recorded resolution. It needs to be enabled in the `.git/config` file. With rerere enabled, a resolution can be recorded for a merge conflict as shown in the following diagram.

![[Pasted image 20230205175058.png]]

Now, if the maintainer of the `main` branch decides to rewind the commit and rebase instead of a three-way merge, the rerere cache information can be used to resolve the conflict automatically. This is represented in the diagram below.

![[Pasted image 20230205175252.png]]