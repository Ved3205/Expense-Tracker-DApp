# Feature Additions to Expense Tracker DApp

## Added Functionality

### 1. Edit Name Feature

**Purpose**: Allows registered users to update their displayed name in the application.

**Implementation Details**:

**Backend (Solidity Smart Contract)**:
```solidity
function updateName(string memory _newName) public {
        // Make sure the user is already registered
        require(people[msg.sender].walletAddress != address(0), "Not registered");

        // Make sure the new name isn't empty
        require(bytes(_newName).length > 0, "Name cannot be empty");

        // Update the name
        people[msg.sender].name = _newName;

        emit NameUpdated(msg.sender, _newName);

    }

// Corresponding event definition added to contract
event NameUpdated(address indexed walletAddress, string newName);
```

**Frontend (React Components)**:
```javascript
// Added state for new name input
const [newName, setNewName] = useState('');

// Name change handler function
const changeName = async () => {
    if (!newName.trim()) {
        alert("Please enter a new name");
        return;
    }
    try {
        const tx = await contract.updateName(newName.trim());
        await tx.wait();
        setName(newName); // Update local state
        setNewName(''); // Clear input
        alert("Name changed successfully!");
        await loadPeople(); // Refresh people list
    } catch (error) {
        console.error("Name update failed:", error);
        alert(`Error changing name: ${error.message}`);
    }
};

// Added to UI render
<div className="change-name">
    <h3>Your Name: {name}</h3>
    <input
        type="text"
        placeholder="Enter new name"
        value={newName}
        onChange={(e) => setNewName(e.target.value)}
    />
    <button onClick={changeName}>Change Name</button>
</div>
```

### 2. Total Registered Users Counter

**Purpose**: Displays the current number of registered users on the platform.

**Implementation Details**:

**Backend (Solidity Smart Contract)**:
```solidity
// Added to ExpenseTracker.sol
function getTotalRegisteredPeople() public view returns (uint256 count) {
    return registeredPeople.length;
}
```

**Frontend (React Components)**:
```javascript
// Added state for total registered count
const [totalRegistered, setTotalRegistered] = useState(0);

// Fetch function to get count from blockchain
const fetchTotalRegisteredPeople = async () => {
    if (!contract) return;
    try {
        const count = await contract.getTotalRegisteredPeople();
        setTotalRegistered(count.toNumber());
    } catch (error) {
        console.error("Failed to fetch total registered users:", error);
    }
};

// Added to UI display
<p>Total Registered Users: {totalRegistered}</p>

// Integrated into existing lifecycle
useEffect(() => {
    // ... existing code ...
    if (registered) {
        // ... existing code ...
        await fetchTotalRegisteredPeople();
    }
}, [contract, account]);

const registerPerson = async () => {
    // ... existing registration code ...
    await fetchTotalRegisteredPeople();
};
```
