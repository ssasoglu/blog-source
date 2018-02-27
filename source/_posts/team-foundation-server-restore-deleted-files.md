---
title: Restore (Undelete) Previously Deleted Files in TFS
date: 2015-08-21 10:53:25
tags:
  - restore
  - team foundation server
  - tfs
  - visual studio
  - vs2013
---

If you want to restore or view history of the deleted files in GUI, you can change the Source Control Explorer settings to display deleted files.

The option may be set using menu Tools=&gt;Options=&gt;Source Control=&gt;Visual Studio Team Foundation, by checking "Show deleted items in the Source Control Explorer" checkbox.

I prepared some screenshots on VS 2013 for guidance. You can see them below:

![TFS-VSOptions](/images/posts/2015/TFS-VSOptions.png)

If you open the location of a deleted file from Source Control Explorer, you will see something like this:

![TFS-DeletedFile](/images/posts/2015/TFS-DeletedFile.png)

Now you can query history of the deleted file.

![TFS-DeletedFileHistory](/images/posts/2015/TFS-DeletedFileHistory.png)

You can also undelete the file from right-click menu. You will need to add undeleted files to your solution manually.