# fastx-doc

## TestNet Calcite

- [ ] No-confirm 退出机制
- [ ] finalizeExit 分成2步退出
- [ ] Tx expireTime 放在退出挑战机制
- [ ] startExit 提供 finalize 的奖金和 withdraw 的奖金，和担保金
- [ ] 节点押金机制
- [ ] 合约增加PoA节点列表，submitBlock 使用超过2/3节点数对区块进行签名，4～20个（需要确定上限数）
- [ ] PoA列表更新方法（超过2/3人数签名可以更新）
- [ ] 合约升级机制（合约升级后，给用户一段Grace Period时间退出）
- [ ] **同一个block处理多个pendingTX：Operator做签名，保证交易会被下一个区块打包。如果发生问题，用户可以挑战区块并获得保证金（使用一个新的挑战方法？），确定保证金比例，确定该区块是否为非法（？）**
- [ ] **子链交易手续费（多加1 input和1 output）**

## Next Step
- [ ] 快速退出市场（结合0x ？）
- [ ] **PoS挖矿机制**
