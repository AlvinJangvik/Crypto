import hashlib
import time

class Block:

    # skapar block, init startar det hela.
    def _init_(self, index, proof_no, prev_hash, data, timestamp=none): # Self betyder att det är detta objektet, det kan vara flera objekt av samma klass
        self.index = index # Index är positionen av varje block i blockchainen
        self.proof_no = proof_no # Hash nummret som kom efter att man skapade blocket
        self.prev_hash = prev_hash # Hash nummeret till det föregående blocket
        self.data = data # Sparar data om alla transaktioner, mängd köpt
        self.Timestamp = timestamp or time.time() # Tid för transaktioner

    
    @property
    def calculate_hash(self): # genererar hash till blocken skapade ovan
        block_of_string = "{}{}{}{}{}".format(self.index, self.proof_no,
                                              self.prev_hash, self.data,
                                              self.timestamp)

        return hashlib.sha256(block_of_string.encode()).hexdigest() # sha256 är en modul som skapar 256-bit hash strängar

    def _repr_(self):
        return "{} - {} - {} - {} - {}".format(self.index, self.proof_no,
                                              self.prev_hash, self.data,
                                              self.timestamp) # Skickar tillbaka info om blocket





class BlockChain:

    ## Skapar en blockchain ##
    def __init__(self):
        self.chain = [] # Variabel som håller alla block
        self.current_data = [] # Håller koll på all meta data om transaktioner
        self.nodes = set()
        self.construct_genesis() # Metod som tar hand om första blocket


    ## Skapar första blocket ##
    def construct_genesis(self):
        self.construct_block(proof_no=0, prev_hash=0) # proof_no är hash nummeret på detta blocket. prev_hash är hash nummeret på förra blocket


    ## Skapar block ##
    def construct_block(self, proof_no, prev_hash):
        block = Block(
            index=len(self.chain), # Längden på kedjan
            proof_no=proof_no, # Sätter denna metodens variabler till de vi skickade från construct genesis
            prev_hash=prev_hash, # ^^
            data=self.current_data) # Håller koll på all gammal data som har blivit borttagen från blocken
        self.current_data = [] # Tar bort senaste transaktions datan så att ny data kan skrivas

        self.chain.append(block) # Lägger till ny block i kedjan, så att de blir kopplade till de tidigare.
        return block; # Skickar tillbaka det ny blocket


    ## Kollar säkerheten ##
    @staticmethod # Statiskmetod = finns bara en verision
    def check_validity(block, prev_block):
        if prev_block.index + 1 != block.index: # Kollar att index nummeret på förra plus 1 är samma som det nuvarande blockets index
            return False

        elif prev_block.calculate_hash != block.prev_hash: # Räknar ut förra blockets hash och sen kolla att meta datan är samma på det nuvarande blocket
            return False

        elif not BlockChain.verifying_proof(block.proof_no, prev_block.proof_no):  # kollar ifall hashen fortfarande har fyra nollor i början
            return False

        elif block.timestamp <= prev_block.timestamp: # Kollar att nuvarande blockets tid den skapades inte är tidigare än förra blockets skapelse tid
            return False

        return True # Skickar tillbaka att blockchainen är säker ifall ingen av scenariorna ovanför var sanna


    ## Läger till ny data ##
    def new_data(self, sender, recipient, quantity):
        self.current_data.append({ # Lägger till info om en ny transaktion till blocket/blocken som har flytttats
            'sender': sender,
            'recipient': recipient,
            'quantity': quantitty
        })
        return True


    ## Bevis på arbete ###
    @staticmethod
    def proof_of_work(last_proof):
        # Denna algoritmen är för att göra det svårt att ändra och fåt controll över blocken
        # Den funkar genom att generare ett nytt nummer som läggs till efter nummeret på förra blocket och använder båda för att generera en ny hash
        # Den kommer fortsätta genere en ny proof tills hashen av de båda har fyra nollor i början.
        proof_no = 0
        while BlockChain.veryfying_proof(proof_no, lastt_proof) is False:
            proof_no += 1 # plussar tills haschen har fyra nollor

        return proof_no


    ## verifiera bevis ##
    @staticmethod
    def verifying_proof(last_proof, proof):
        # Verifierar ifall en hash har fyra nollor i början
        # Denna metod används av både de som minear och av programmet när den skapar ett nytt block

        guess = f'{last_proof}{proof}'.encode() # Sätter ihop båda variablerna
        guess_hash = hashlib.sha256(guess).hexdigest() # Omvandlar dem till en hash
        return guess[:4] == "0000" # Retunerar sant eller falskt ifall hashen har 4 nollor i början eller inte


    ## Hämta senaste blocket ##
    @property # property ger metoden get och set vilket gör att andra metoder kan hämta och ändra dess info
    def latest_block(self):
        return self.chain[-1]


    ##############################################################
    ##############################################################
    ##############################################################
    ##############################################################
    def block_mining(self, details_miner):

        self.new_data(
            sender="0",  # Antyder att denna noden( denna datastrukturen ) har skapat ett block
            receiver=details_miner,
            quantity=
            1,  # Lyckas indentifiera proof nummeret så blir man belönad med ett
        )

        last_block = self.latest_block

        last_proof_no = last_block.proof_no
        proof_no = self.proof_of_work(last_proof_no)

        last_hash = last_block.calculate_hash
        block = self.construct_block(proof_no, last_hash)

        return vars(block)

    def create_node(self, address):
        self.nodes.add(address)
        return True

    @staticmethod
    def obtain_block_object(block_data):
        #obtains block object from the block data

        return Block(
            block_data['index'],
            block_data['proof_no'],
            block_data['prev_hash'],
            block_data['data'],
            timestamp=block_data['timestamp'])
