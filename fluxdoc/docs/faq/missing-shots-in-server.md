# 服务器明显比官匹空枪问题的解释

由Flux HvH管理组编写。

## 什么是扩散？

当玩家开枪的时候，子弹并不会百分之百的向你开枪的那一点上打过去，而是会进行随机的偏移。这个随机的偏移，我们便称为扩散 (Spread)。

扩散会随着你的移动状态而改变，例如在空中时会显著增大扩散，移动速度也会明显影响扩散。

## 什么是命中率？

由于扩散的存在，如果没有游戏漏洞可以做到无扩散的话，是不可能做到指哪打哪的。所以作弊便引入了命中率 (Hit Chance) 这一概念。

命中率，顾名思义，是作弊预测的射出子弹能够命中目标的概率。但是此处需要格外注意一点：是作弊预测的概率！这个概率不是准确的概率，因为这个概率是作弊根据各种状态以及算法预测的概率。所以，即便你把命中率设置为100%，那也是作弊预测的概率，依然会存在打不中的现象。

命中率只是一个预测的值，并不能够百分之百准确。

## 为什么会有无扩散？

Valve在开发CS2的时候，由于Sub-Tick的引入，所以想到了客户端进行动画预测的一系列主意，Valve本意是想通过这类算法，显著降低延迟对玩家游玩体验带来的影响。这点从最近更新的本地动画预测更能体现出来。

利用Valve刚刚更新的本地动画预测来举例子。当本地玩家开枪时，在开枪的一瞬间，如果击中了，本地就会播放敌人受到伤害的动画。但是上文已经提到，扩散是随机的，本地理论上是无法预测扩散导致子弹的实际落点的，怎么才能够知道客户端是不是真的打中了呢？所以由此可见，Valve为了实现这些操作，将服务器与客户端的随机数种子同步了。

简单解释一下随机数种子：现在计算机产生的随机数，实际上大多都是伪随机数。用通俗易懂的话来解释，即为计算机通过一串已经确定的数字，经过复杂的计算，生成出来一个数字。而这串已经确定的数字，我们就称其为随机数种子。如果你觉得以上解释难以理解，你只需要记住：当种子已经被确定，那么所有的随机数都可以被预测。

因此，由于服务器与客户端的随机数种子同步，客户端自己就可以预测自己的子弹落点是哪里。所以作弊根据以上原理，当开枪时使用一些方式将扩散抵消掉，就能够实现无扩散了。

## 当服务端客户端随机数种子不同步时会发生什么？

但是实际上，通过上文这种方式进行补偿实现的无扩散是可以被轻松检测的。这就是为什么在官匹之前可以两发无扩散，现在可以十发无扩散的原因。当官匹服务器检测到你在使用无扩散后，将会重置你的随机数种子。此时因为随机数种子不再同步，因此就无法使用无扩散了。同时开枪 (包括其他人和自己) 的本地弹道与弹孔都是不准确的，因为在CS2中子弹射出的特效计算也都是由随机数种子计算的。如果你开启了本地动画预测功能，也是完全不准确的。

所以现在，Valve只有当检测到你使用了无扩散这类功能后才会为你更换随机数种子，而不是开始直接不同步随机数种子。但是在HvH的社区服务器中，由于必须要禁止无扩散这一功能，所以必须起初就立刻更换随机数种子。您也不想在服务器里被新加入的玩家飞两发吧？

## 现在的作弊是如何实现无扩散的？

上文中提到，通过随机数种子补偿的方法实现完全无扩散是很容易被检测的，所以现在的作弊都有移除扩散 (Remove Spread) 功能和强制移除扩散 (Force Remove Spread) 功能。在社区服务器中，移除扩散功能启动时，强制移除扩散功能一般是不可用的，因为没有必要这么做。但是在官匹中，两个功能则是完全不同的。

当移除扩散功能开启后，移除掉的扩散是通过另一种方法实现的，同样依赖于随机数种子。这种方法相对来说没有上面提到的方法那么简单粗暴有效，但是能够绕过Valve的检测，实现尽可能降低扩散的效果。当武器扩散较小时，效果很好。但武器扩散较大 (例如在空中) 时，效果则一般甚至打不中。这就是移除扩散和强制移除扩散的区别。

总体来说就是效果较差，但是不会被Valve检测到。但是当你使用强制移除扩散，Valve检测到然后更换了随机数种子之后，移除扩散也会失效。

## 所以当移除扩散开启后，命中率还生效吗？

无效。

你已经可以实现指哪打哪了，为什么还要考虑命中率？无论是移除扩散还是强制移除扩散启用后，命中率没有任何作用。

## 所以为什么官匹不空枪服务器空枪？

因为在官匹中，你依赖随机数种子同步实现了移除扩散，此时你几乎没有武器扩散，所以能够指哪打哪，没有命中率的说法。而在服务器中，随机数种子始终是不同步的，所以此时是有命中率这一说法的。

如果要验证我的说法，那么你可以去官匹中使用强制无扩散打完10发子弹，Valve将会为你更换随机数种子。此时再去测试一下是不是像禁止无扩散的服务器一样空枪就可以了。