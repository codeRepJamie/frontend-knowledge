#中介者模式
> Mediator Pattern

##知识点一：定义
 中介者模式就是程序里面各个模式的的沟通中心，减少每个模块互相引用。Mediator模式本质上是Observer模式的共享目标。使用唯一一个目标，控制所有的观察者。著名的例子有DOM事件冒泡和事件委托（事件捕获）、React的Redux就是典型的中介者模式，即所有的订阅针对的是document而不是单个node节点，这个高级别的对象(document对象)承担了向所有订阅者（node节点对象）通知有关交互事件责任，而不是靠手动绑定到每一个节点事件。
 
 中介者模式定义：使用单一对象处理其他涉及不同对象的流程，仅靠单一对象集中处理，使得流程更容易理解和维护。

## 知识点二：一个中介者模式例子

1.  没有使用中介模式实现


```js
// 先定义玩家构造函数
function Player(name, teamColor){
    this.partners = []; // 队友列表
    this.enemies = []; // 敌人列表
    this.state = 'live'; // 玩家状态
    this.name = name; // 角色名字
    this.teamColor = null; // 队伍颜色
};

// 玩家团队胜利
Player.prototype.win = function(){ 
    console.log('winner:' + this.name);
};

// 玩家团队失败
Player.prototype.lose = function(){ 
    console.log('loser:' + this.name);
};

// 玩家死亡
Player.prototype.die = function(){
    var all_dead = true;
    
    this.state = 'dead';
    
    // 遍历队友列表
    for(var i=0, partner; partner = this.partners[i++];){
        if(partner.state !== 'dead'){
            all_dead = false;
            break;
        }
    }
    
    // 如果队友全部死亡
    if(all_dead === true){
        this.lose();
        
        // 通知所有队友玩家游戏失败
        for(var i=0, partner; partner = this.partners[i++];){
            partner.lose();
        }
        
        // 通知所有敌人游戏胜利
        for(var i=0, enemy; enmy = this.enemise[i++];){
            enemy.win();
        }
    }
};

// 定义数组保存所有玩家
var players = [];
// 定义一个工厂来创建玩家
var playerFactory = function(name, teamColor){
    // 创建新玩家
    var newPlayer = new Player(name, teamColor);
    
    // 通知所有玩家有新角色加入（工厂必须要知道如何调用Player类）
    for(var i=0,player; player=players[i++];){
        if(player.teamColor === newPlayer.teamColor){ // 同一队玩家
            player.partners.push(newPlayer); // 互相添加到队友列表
            newPlayer.partner.push(player);
        }else{
            player.enemies.push(newPlayer); // 互相添加到敌人列表
            newPlayer.enemies.push(player);
        }
    }
    
    players.push(newPlayer);
    
    return newPlayer;
};

// 创建8个玩家对象
var player1 = playerFactory('a', 'red');
var player2 = playerFactory('b', 'red');
var player3 = playerFactory('c', 'red');
var player4 = playerFactory('d', 'red');

var player5 = playerFactory('e', 'blue');
var player6 = playerFactory('f', 'blue');
var player7 = playerFactory('g', 'blue');
var player8 = playerFactory('h', 'blue');
```

1.  使用中介者模式改造

```js
// 先定义玩家构造函数

function Player(name, teamColor){
    this.name = name; // 角色名字
    this.teamColor = teamColor; // 队伍颜色
    this.state = 'alive'; // 玩家生存状态
}

Player.prototype.win = function(){ 
    console.log('winner:' + this.name);
};

Player.prototype.lose = function(){ 
    console.log('loser:' + this.name);
};

Player.prototype.die = function(){
    this.state = 'dead';
    playerDirector.ReceiveMessage('playerDead', this); // 给中介者发送消息，玩家死亡
};

Player.prototype.remove = function(){
    playerDirector.ReceiveMessage('removePlayer', this);  // 给中介者发送消息，移除一个玩家
};

Player.prototype.changeTeam = function(){
    playerDirector.ReceiveMessage('changeTeam', this); // 给中介者发送消息，玩家换队
};

// 实现playerDirector对象
var playDirector = (function(){
    var players = {}; // 保存所有玩家
    var operations = {}; // 中介者可以执行的操作
    
    // 新增一个玩家
    operations.add = function(player){
        var teamColor = player.teamColor;
        players[teamColor] = players[teamColor] || [];
        players[teamColor].push(player);
    };
    
    // 移除一个玩家
    operations.removePlayer = function(player){
        var teamColor = player.teamColor;
        var teamPlayers = players[teamColor] || [];
        
        for(var i=teamPlayers.length - 1; i >= 0 ;i --){
            if(teamPlayers[i] === player){
                teamPlayers.splice(i, 1);
            }
        }
    };
    
    // 玩家换队
    operations.changeTeam = function(player, newTeamColor){
        operations.removePlayer(player); // 从原队伍中删除
        player.teamColor = newTeamColor; // 换颜色
        operations.addPlayer(player); // 新增玩家到新的队伍
    }
    
    operations.playerDead = function(player){
        var teamColor = player.teamColor;
        var teamPlayer = players[teamColor];
        
        var all_dead = true;
        
        // 遍历队友列表
        for(var i=0, player; player = teamPlayer[i++];){
            if(player.state !== 'dead'){
                all_dead = false;
                break;
            }
        }
        
        // 如果队友全部死亡
        if(all_dead === true){
            this.lose();
            
            // 通知所有队友玩家游戏失败
            for(var i=0, player; player = teamPlayer[i++];){
                player.lose();
            }
            
            // 通知所有敌人游戏胜利
            for(var color in players){
                if(color !== teamColor){
                    var teamPlayers = players[color];
                    for(var i=0, player; player = teamPlayers[i++];){
                        player.win();
                    }
                }
            }
        }
    }
    
    var ReceiveMessage = function(){
        var message = Array.prototype.shift.call(arguments);
        operations[message].apply(this, arguments);
    };
    
    return {
        ReciveMessage: ReceiveMessage
    }
})();

var playerFactory = function(name, teamColor){
    var newPlayer = new Player(name, teamColor);
    // 给中介者发送消息，新增玩家（工厂只通过中介playerDirector发送消息和参数，完全不需要关心Player类是怎样操作）
    playerDirector.ReceiveMessage('addPlayer', newPlayer); 
    
    return newPlayer;
};

// 创建8个玩家对象
var player1 = playerFactory('a', 'red');
var player2 = playerFactory('b', 'red');
var player3 = playerFactory('c', 'red');
var player4 = playerFactory('d', 'red');

var player5 = playerFactory('e', 'blue');
var player6 = playerFactory('f', 'blue');
var player7 = playerFactory('g', 'blue');
var player8 = playerFactory('h', 'blue');
```

事例中，playDirector就是扮演中介者的角色，负责Player类实例对象与工厂之间的消息联系。使得工厂方法与Player类耦合程度降低。例如，如果要修改Player的实现方法，只需要更改Player类与中介者的代码即可。

##知识点四：特点
优点：
1.  系统中的对象/组件通讯对象，从过去多对多减少为多对一，解耦程度较高，发布者与订阅者之间的维护相对容易
1.  减轻模块之间异常处理压力。例如，如果复杂的模块之间是直接相互依赖通讯，当其中一个模块出现异常时，很容易导致其余模块出现多米诺效应，然而这个问题解耦在中介者里处理，就很容易
缺点：
1.  中介者最大缺点就是把各个模块的复杂性转移到中介者本身。通常中介者本身就是一个难以维护的对象
1.  性能下降，由于模块之间的通讯总是间接进行的，多了一个中介者就是多了一层消耗

##知识点五：中介者模式 vs 观察者模式的差异
1.  观察者模式非单一对象处理。观察者模式比较自由，观察者模式是通过创建观察者对象和目标对象进行通信交互，单一目标通常有非常多观察者，有时一个观察者可能是另一个观察者的目标。

[^1]: [JavaScript设计模式与开发实践-中介者模式](https://www.kancloud.cn/wengwang/read_1/436085)

