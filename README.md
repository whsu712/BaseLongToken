# BaseLongToken
BaseLongToken
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Burnable.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Permit.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

contract BaseLongToken is ERC20, ERC20Burnable, ERC20Permit, Ownable, ReentrancyGuard {

    uint256 public constant MAX_SUPPLY = 1000000000 * 10 ** 18;
    uint256 public taxRate = 200; // 2% tax
    address public taxWallet;
    bool public tradingOpen = false;
    mapping(address => bool) public isExcludedFromTax;

    event TaxRateUpdated(uint256 newRate);
    event TaxWalletUpdated(address newWallet);
    event TradingStatusChanged(bool status);

    constructor() 
        ERC20("BaseLongToken", "BLT") 
        ERC20Permit("BaseLongToken") 
        Ownable(msg.sender) 
    {
        taxWallet = msg.sender;
        isExcludedFromTax[msg.sender] = true;
        isExcludedFromTax[address(this)] = true;
        
        _mint(msg.sender, MAX_SUPPLY);
    }

    function _transfer(
        address sender,
        address recipient,
        uint256 amount
    ) internal virtual override {
        if (!tradingOpen && sender != owner() && recipient != owner()) {
            revert("Trading is not open yet");
        }

        uint256 taxAmount = 0;
        
        if (!isExcludedFromTax[sender] && !isExcludedFromTax[recipient] && taxRate > 0) {
            taxAmount = (amount * taxRate) / 10000;
        }

        if (taxAmount > 0) {
            super._transfer(sender, taxWallet, taxAmount);
            amount = amount - taxAmount;
        }

        super._transfer(sender, recipient, amount);
    }

    function setTaxRate(uint256 newRate) external onlyOwner {
        require(newRate <= 1000, "Tax rate cannot exceed 10%");
        taxRate = newRate;
        emit TaxRateUpdated(newRate);
    }

    function setTaxWallet(address newWallet) external onlyOwner {
        require(newWallet != address(0), "Cannot set zero address");
        taxWallet = newWallet;
        emit TaxWalletUpdated(newWallet);
    }

    function excludeFromTax(address account, bool excluded) external onlyOwner {
        isExcludedFromTax[account] = excluded;
    }

    function openTrading() external onlyOwner {
        tradingOpen = true;
        emit TradingStatusChanged(true);
    }

    function closeTrading() external onlyOwner {
        tradingOpen = false;
        emit TradingStatusChanged(false);
    }

    function mint(address to, uint256 amount) external onlyOwner {
        require(totalSupply() + amount <= MAX_SUPPLY, "Exceeds max supply");
        _mint(to, amount);
    }

    function burnFrom(address account, uint256 amount) public override onlyOwner {
        _burn(account, amount);
    }

    function withdrawETH() external onlyOwner nonReentrant {
        payable(owner()).transfer(address(this).balance);
    }

    receive() external payable {}
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Burnable.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Permit.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/ReentrancyGuard.sol";

contract BaseLongToken is ERC20, ERC20Burnable, ERC20Permit, Ownable, ReentrancyGuard {

    uint256 public constant MAX_SUPPLY = 1000000000 * 10 ** 18;
    uint256 public taxRate = 200; // 2% tax
    address public taxWallet;
    bool public tradingOpen = false;
    mapping(address => bool) public isExcludedFromTax;

    event TaxRateUpdated(uint256 newRate);
    event TaxWalletUpdated(address newWallet);
    event TradingStatusChanged(bool status);

    constructor() 
        ERC20("BaseLongToken", "BLT") 
        ERC20Permit("BaseLongToken") 
        Ownable(msg.sender) 
    {
        taxWallet = msg.sender;
        isExcludedFromTax[msg.sender] = true;
        isExcludedFromTax[address(this)] = true;
        
        _mint(msg.sender, MAX_SUPPLY);
    }

    function _transfer(
        address sender,
        address recipient,
        uint256 amount
    ) internal virtual override {
        if (!tradingOpen && sender != owner() && recipient != owner()) {
            revert("Trading is not open yet");
        }

        uint256 taxAmount = 0;
        
        if (!isExcludedFromTax[sender] && !isExcludedFromTax[recipient] && taxRate > 0) {
            taxAmount = (amount * taxRate) / 10000;
        }

        if (taxAmount > 0) {
            super._transfer(sender, taxWallet, taxAmount);
            amount = amount - taxAmount;
        }

        super._transfer(sender, recipient, amount);
    }

    function setTaxRate(uint256 newRate) external onlyOwner {
        require(newRate <= 1000, "Tax rate cannot exceed 10%");
        taxRate = newRate;
        emit TaxRateUpdated(newRate);
    }

    function setTaxWallet(address newWallet) external onlyOwner {
        require(newWallet != address(0), "Cannot set zero address");
        taxWallet = newWallet;
        emit TaxWalletUpdated(newWallet);
    }

    function excludeFromTax(address account, bool excluded) external onlyOwner {
        isExcludedFromTax[account] = excluded;
    }

    function openTrading() external onlyOwner {
        tradingOpen = true;
        emit TradingStatusChanged(true);
    }

    function closeTrading() external onlyOwner {
        tradingOpen = false;
        emit TradingStatusChanged(false);
    }

    function mint(address to, uint256 amount) external onlyOwner {
        require(totalSupply() + amount <= MAX_SUPPLY, "Exceeds max supply");
        _mint(to, amount);
    }

    function burnFrom(address account, uint256 amount) public override onlyOwner {
        _burn(account, amount);
    }

    function withdrawETH() external onlyOwner nonReentrant {
        payable(owner()).transfer(address(this).balance);
    }

    receive() external payable {}
}
