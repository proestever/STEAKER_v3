//
//           _____                _____                    _____                    _____                    _____                    _____                    _____
//          /\    \              /\    \                  /\    \                  /\    \                  /\    \                  /\    \                  /\    \
//         /::\    \            /::\    \                /::\    \                /::\    \                /::\____\                /::\    \                /::\    \
//        /::::\    \           \:::\    \              /::::\    \              /::::\    \              /:::/    /               /::::\    \              /::::\    \
//       /::::::\    \           \:::\    \            /::::::\    \            /::::::\    \            /:::/    /               /::::::\    \            /::::::\    \
//      /:::/\:::\    \           \:::\    \          /:::/\:::\    \          /:::/\:::\    \          /:::/    /               /:::/\:::\    \          /:::/\:::\    \
//     /:::/__\:::\    \           \:::\    \        /:::/__\:::\    \        /:::/__\:::\    \        /:::/____/               /:::/__\:::\    \        /:::/__\:::\    \
//     \:::\   \:::\    \          /::::\    \      /::::\   \:::\    \      /::::\   \:::\    \      /::::\    \              /::::\   \:::\    \      /::::\   \:::\    \
//   ___\:::\   \:::\    \        /::::::\    \    /::::::\   \:::\    \    /::::::\   \:::\    \    /::::::\____\________    /::::::\   \:::\    \    /::::::\   \:::\    \
//  /\   \:::\   \:::\    \      /:::/\:::\    \  /:::/\:::\   \:::\    \  /:::/\:::\   \:::\    \  /:::/\:::::::::::\    \  /:::/\:::\   \:::\    \  /:::/\:::\   \:::\____\
// /::\   \:::\   \:::\____\    /:::/  \:::\____\/:::/__\:::\   \:::\____\/:::/  \:::\   \:::\____\/:::/  |:::::::::::\____\/:::/__\:::\   \:::\____\/:::/  \:::\   \:::|    |
// \:::\   \:::\   \::/    /   /:::/    \::/    /\:::\   \:::\   \::/    /\::/    \:::\  /:::/    /\::/   |::|~~~|~~~~~     \:::\   \:::\   \::/    /\::/   |::::\  /:::|____|
//  \:::\   \:::\   \/____/   /:::/    / \/____/  \:::\   \:::\   \/____/  \/____/ \:::\/:::/    /  \/____|::|   |           \:::\   \:::\   \/____/  \/____|:::::\/:::/    /
//   \:::\   \:::\    \      /:::/    /            \:::\   \:::\    \               \::::::/    /         |::|   |            \:::\   \:::\    \            |:::::::::/    /
//    \:::\   \:::\____\    /:::/    /              \:::\   \:::\____\               \::::/    /          |::|   |             \:::\   \:::\____\           |::|\::::/    /
//     \:::\  /:::/    /    \::/    /                \:::\   \::/    /               /:::/    /           |::|   |              \:::\   \::/    /           |::| \::/____/
//      \:::\/:::/    /      \/____/                  \:::\   \/____/               /:::/    /            |::|   |               \:::\   \/____/            |::|  ~|
//       \::::::/    /                                 \:::\    \                  /:::/    /             |::|   |                \:::\    \                |::|   |
//        \::::/    /                                   \:::\____\                /:::/    /              \::|   |                 \:::\____\               \::|   |
//         \::/    /                                     \::/    /                \::/    /                \:|   |                  \::/    /                \:|   |
//          \/____/                                       \/____/                  \/____/                  \|___|                   \/____/                  \|___|
//
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/utils/math/SafeMath.sol";

contract StakingContract {
    using SafeMath for uint256;

    string public name;
    string public symbol;
    uint8 public decimals;

    struct Stake {
        uint256 amount;
        uint256 startTime;
        uint256 endTime;
        bool active;
        uint256 penaltyAmount;
    }

    mapping(address => Stake[]) private stakes;
    mapping(address => mapping(address => uint256)) private allowances;
    mapping(address => uint256) private balances;

    uint256 private totalSupply;

    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
    event StakeStarted(address indexed staker, uint256 amount, uint256 endTime);
    event StakeEnded(address indexed staker, uint256 amount, uint256 penaltyAmount);

    constructor() {
        name = "Staking Token";
        symbol = "STK";
        decimals = 18;
        totalSupply = 1e12 * (10**uint256(decimals));
        balances[msg.sender] = totalSupply;
    }

    function balanceOf(address account) public view returns (uint256) {
        return balances[account];
    }

    function transfer(address recipient, uint256 amount) public returns (bool) {
        _transfer(msg.sender, recipient, amount);
        return true;
    }

    function transferFrom(address sender, address recipient, uint256 amount) public returns (bool) {
        uint256 currentAllowance = allowances[sender][msg.sender];
        require(currentAllowance >= amount, "Insufficient allowance");
        _transfer(sender, recipient, amount);
        _approve(sender, msg.sender, currentAllowance.sub(amount));
        return true;
    }

    function approve(address spender, uint256 amount) public returns (bool) {
        _approve(msg.sender, spender, amount);
        return true;
    }

    function allowance(address owner, address spender) public view returns (uint256) {
        return allowances[owner][spender];
    }

    function createStake(uint256 amount, uint256 duration) external {
        uint256 stakeAmount = amount.mul(10**uint256(decimals));  // Convert to smallest unit
        require(stakeAmount > 0 && balances[msg.sender] >= stakeAmount, "Insufficient balance");
        uint256 endTime = block.timestamp.add(duration.mul(1 days));
        balances[msg.sender] = balances[msg.sender].sub(stakeAmount);
        totalSupply = totalSupply.sub(stakeAmount);

        emit Transfer(msg.sender, address(0), stakeAmount); // Burn tokens

        stakes[msg.sender].push(Stake(stakeAmount, block.timestamp, endTime, true, 0));
        emit StakeStarted(msg.sender, stakeAmount, endTime);
    }

    function endStake(uint256 stakeIndex) external {
        require(stakeIndex < stakes[msg.sender].length, "Invalid stake index");

        Stake storage stake = stakes[msg.sender][stakeIndex];
        require(stake.active, "Stake already ended");

        uint256 penaltyAmount = calculatePenalty(stake);
        stake.amount = stake.amount.sub(penaltyAmount);
        balances[msg.sender] = balances[msg.sender].add(stake.amount);
        totalSupply = totalSupply.add(stake.amount);

        stake.active = false;
        stake.penaltyAmount = penaltyAmount;

        emit Transfer(address(0), msg.sender, stake.amount); // Mint tokens
        emit StakeEnded(msg.sender, stake.amount, penaltyAmount);
    }

    function calculatePenalty(Stake storage stake) internal view returns (uint256) {
        if (block.timestamp >= stake.endTime) {
            return 0; // No penalty if stake has matured
        }
        
        uint256 totalStakingTime = stake.endTime.sub(stake.startTime);
        uint256 timeServed = block.timestamp.sub(stake.startTime);
        uint256 timeRemaining = totalStakingTime.sub(timeServed);

        // Penalty is proportionate to the time remaining in the stake
        uint256 penaltyAmount = stake.amount.mul(timeRemaining).div(totalStakingTime);

        return penaltyAmount;
    }

    function _transfer(address sender, address recipient, uint256 amount) internal {
        require(sender != address(0), "Transfer from the zero address");
        require(recipient != address(0), "Transfer to the zero address");

        balances[sender] = balances[sender].sub(amount);
        balances[recipient] = balances[recipient].add(amount);

        emit Transfer(sender, recipient, amount);
    }

    function _approve(address owner, address spender, uint256 amount) internal {
        require(owner != address(0), "Approve from the zero address");
        require(spender != address(0), "Approve to the zero address");

        allowances[owner][spender] = amount;

        emit Approval(owner, spender, amount);
    }

    function getStakes(address account) public view returns (Stake[] memory) {
        return stakes[account];
    }

    function getTotalStakes(address account) public view returns (uint256) {
        return stakes[account].length;
    }
}
