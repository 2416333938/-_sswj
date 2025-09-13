//这是一个使用turbowarp（scratch的魔改版）制作的游戏，使用[WSAD]移动，[空格]回血,[Home]重新开始游戏
//这只是它的第一个版本，以后它还会再跟新
//以下是它使用的一些自定义扩展
(function(Scratch) {
    'use strict';

    if (!Scratch.extensions.unsandboxed) {
        throw new Error('此扩展需要不受沙箱限制的环境');
    }

    class DirectionDistanceCalculator {
        constructor() {
            // 扩展初始化代码
        }

        /**
         * 返回扩展元数据
         */
        getInfo() {
            return {
                id: 'directionDistanceCalculator',
                name: '方向与距离计算器',
                blocks: [
                    {
                        opcode: 'getDirection',
                        blockType: Scratch.BlockType.REPORTER,
                        text: '点 (x1: [X1], y1: [Y1]) 面向点 (x2: [X2], y2: [Y2]) 的方向',
                        arguments: {
                            X1: {
                                type: Scratch.ArgumentType.NUMBER,
                                defaultValue: 0
                            },
                            Y1: {
                                type: Scratch.ArgumentType.NUMBER,
                                defaultValue: 0
                            },
                            X2: {
                                type: Scratch.ArgumentType.NUMBER,
                                defaultValue: 100
                            },
                            Y2: {
                                type: Scratch.ArgumentType.NUMBER,
                                defaultValue: 100
                            }
                        }
                    },
                    {
                        opcode: 'getDistance',
                        blockType: Scratch.BlockType.REPORTER,
                        text: '点 (x1: [X1], y1: [Y1]) 到点 (x2: [X2], y2: [Y2]) 的距离',
                        arguments: {
                            X1: {
                                type: Scratch.ArgumentType.NUMBER,
                                defaultValue: 0
                            },
                            Y1: {
                                type: Scratch.ArgumentType.NUMBER,
                                defaultValue: 0
                            },
                            X2: {
                                type: Scratch.ArgumentType.NUMBER,
                                defaultValue: 100
                            },
                            Y2: {
                                type: Scratch.ArgumentType.NUMBER,
                                defaultValue: 100
                            }
                        }
                    }
                ]
            };
        }

        /**
         * 计算两点之间的方向（返回-180到180度之间的角度）
         * @param {object} args - 包含x1, y1, x2, y2的参数对象
         * @returns {number} 方向角度（度）
         */
        getDirection(args) {
            const x1 = args.X1;
            const y1 = args.Y1;
            const x2 = args.X2;
            const y2 = args.Y2;

            // 计算两点之间的差值
            const dx = x2 - x1;
            const dy = y2 - y1;

            // 计算弧度
            let radians = Math.atan2(dy, dx);
            
            // 转换为角度
            let degrees = radians * (180 / Math.PI);
            
            // 调整角度，使其符合Scratch的方向系统（0度向上，顺时针增加）
            degrees = (90 - degrees) % 360;
            
            // 转换为-180到180度的表示方式
            if (degrees > 180) {
                degrees -= 360;
            }
            
            return degrees;
        }

        /**
         * 计算两点之间的距离
         * @param {object} args - 包含x1, y1, x2, y2的参数对象
         * @returns {number} 两点之间的直线距离
         */
        getDistance(args) {
            const x1 = args.X1;
            const y1 = args.Y1;
            const x2 = args.X2;
            const y2 = args.Y2;

            // 计算两点之间的差值
            const dx = x2 - x1;
            const dy = y2 - y1;

            // 使用勾股定理计算距离
            const distance = Math.sqrt(dx * dx + dy * dy);
            
            return distance;
        }
    }

    // 注册扩展
    Scratch.extensions.register(new DirectionDistanceCalculator());
})(Scratch);



(function(Scratch) {
    'use strict';

    // 帧率监测所需的变量
    let lastTime = 0;
    let frameCount = 0;
    let currentFPS = 0;

    // 更新帧率的函数
    function updateFPS(timestamp) {
        if (lastTime === 0) {
            lastTime = timestamp;
        }
        
        const deltaTime = timestamp - lastTime;
        frameCount++;
        
        // 每秒钟计算一次帧率
        if (deltaTime >= 1000) {
            currentFPS = Math.round((frameCount * 1000) / deltaTime);
            frameCount = 0;
            lastTime = timestamp;
        }
        
        // 继续监测下一帧
        requestAnimationFrame(updateFPS);
    }

    // 开始帧率监测
    requestAnimationFrame(updateFPS);

    class FPSMonitor {
        // 扩展信息
        getInfo() {
            return {
                id: 'fpsmonitor',
                name: '帧率监控',
                color1: '#4a90e2',
                color2: '#3a7bc8',
                blocks: [
                    {
                        opcode: 'getFPS',
                        blockType: Scratch.BlockType.REPORTER,
                        text: '舞台帧率',
                        arguments: {}
                    }
                ]
            };
        }

        // 返回当前计算的帧率
        getFPS() {
            return currentFPS;
        }
    }

    // 注册扩展
    Scratch.extensions.register(new FPSMonitor());
})(Scratch);





class GetIdExtension {
    // 扩展元数据
    getInfo() {
        return {
            id: 'getidextension',
            name: '角色ID',
            color1: '#4a548c',
            color2: '#3a4069',
            color3: '#2d3250',
            blocks: [
                {
                    opcode: 'getMyId',
                    blockType: Scratch.BlockType.REPORTER,
                    text: '我的唯一ID'
                }
            ]
        };
    }

    // 获取当前角色/克隆体的ID
    getMyId(args, util) {
        // 在TurboWarp中，每个角色实例(包括克隆体)都有唯一的id属性
        return util.target.id;
    }
}

// 注册扩展
Scratch.extensions.register(new GetIdExtension());
