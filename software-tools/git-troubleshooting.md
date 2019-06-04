# Git Troubleshooting

Sometimes Git can be annoying.

## Cannot Unlink File

If you try to pull and get an error saying "cannot unlink file", this means that Git failed to update a file because it is locked by another program and Git cannot modify it. The solution is to close any programs that might have that file open.

For example with Altium Designer, the program might not appear to be running but it might have a process still running in the background. Open the Task Manager and end the process called "X2.EXE", which belongs to Altium Designer. Then try to Git pull again, which should work now.
