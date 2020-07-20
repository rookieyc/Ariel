# Ariel

```properties
pragma solidity 0.6.4;

contract MF_1 {
    
    address private owner;
    string private pk;
    
    constructor() public { 
        owner = msg.sender;
        pk = ""; // remember to set data
    }
    
    modifier onlyOwner {
        require(msg.sender == owner, "invalid caller");
        _;
    }
    
    function setKey(string memory new_pk) public onlyOwner {
        pk = new_pk;
    }
    
    function getKey() public view returns (string memory) {
        return pk;
    }
    
    /* ----- */
    
    mapping (string => string []) private model_bks; // model > [bk_1, bk_2]
    mapping (string => Data) private bk_data; // bk_1 > data
    
    struct Data {
        string id;
        string ip;
        string port;
        string information; // e.g. location
        bool isVaild;
    }
    
    function new_a_bk (string memory id, string memory ip, string memory port, string memory information) public onlyOwner returns(bool) {
        Data memory data = Data (id, ip, port, information, true);
    	bk_data[id] = data;
    }
    
    function model_add_a_bk (string memory model, string memory bk_id) public onlyOwner {
        model_bks[model].push(bk_id);
    }
    
    function model_delete_a_bk (string memory model, string memory bk_id) public onlyOwner returns (bool) {
        for (uint i = 0; i < model_bks[model].length; i++) {
            if (compareStrings(model_bks[model][i], bk_id)) {
                    delete model_bks[model][i];
                    return true;
            }
        }
        return false;
    }
    
    function service (string memory model) public view returns (string memory) {
        string memory output = "";
        string [] memory bks = model_bks[model];
        
        for (uint i = 0; i < bks.length; i++) {
            string memory tmp;
            
            Data memory data = bk_data[bks[i]];
            
            if (data.isVaild) {
                tmp = concatenate(tmp, data.id);
                tmp = concatenate(tmp, ":");
                tmp = concatenate(tmp, data.ip);
                tmp = concatenate(tmp, "-");
                tmp = concatenate(tmp, data.port);
                tmp = concatenate(tmp, "-");
                tmp = concatenate(tmp, data.information);
                tmp = concatenate(tmp, "/");
            
                output = concatenate(output, tmp);
            } else {
                tmp = "";
            }
        }
        return output;
    }
    
    function concatenate (string memory a, string memory b) private pure returns (string memory) {
        return string (abi.encodePacked(a, b));
    }
    
    function compareStrings (string memory a, string memory b) private pure returns (bool) {
        return (keccak256(abi.encodePacked((a))) == keccak256(abi.encodePacked((b))));
    }
}
```
