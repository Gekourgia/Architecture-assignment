
First assignment for Course: Architecture of advanced Computer Systems


### 1.  
Τα βασικα στοιχεια που δινονται απο το example αρχειο ειναι τα εξης:

Τυπος __CPU__: 
* AtomicSimpleCPU,
* MinorCPU,
* hpi.  
Συμφωνα με την εντολη , εμεις χρησιμοποιησαμε το MinorCPU.

__Cache__:  
L1 L2 cache με fixed cache line size στα 64 bytes  
η L1 private και η L2 shared

Στα __κομμάτια__ του συστήματος του παραδειγματος δινονται:  
* Το Voltage Domain= 3.3 V
* Το  Clock Domain = 1GHz
* cpu.cluster.voltage =1.2 V
* cpu.cluster.clk_domain = 4GHz
* Num.Of.Cores = (default) 1
* Memory type = DDR3_1600_8x8
* mem-channels = 2
* Mem-size = 2Gb

### 2.  
_a_)  
Εχουμε συμφωνα με τα αρχεια :  
* clock = 1000        :Line 44   Αρα και (1/1000ticks = 1/[10^(-9)] = 1Ghz)
* Cpu type = MinorCPU :Line 65
* Voltage = 3.3V     :Line 1652
* Cpu_cluster.Voltage = 1.2V :Line 1339
* cpu_cluster.clk = 250      :Line 58    Αρα και  (1/250ticks = 4/[10^(-9)] = 4Ghz)
* dcache.tags = 64  :Line 213
* numThreads = 1  :Line 113

_b_)
Στο αρχειο stats.txt αναφερει πως :  
* Line:14  5028 commited instructions
* Line:15 5834 nmbr of ops commited

Μπορω να καταλαβω μονο πως εχουν διαφορετικο αριθμο γιατι το commitedOps περιλαμβανει και τα micro ops.

_c_)  
Απο το stats.txt :  
Number of data accesses 7884  Line:623

### 3.  

Παραδείγματα μοντέλων in-order CPUs που χρησιμοποιει ο Gem5 ειναι τα παρακατω:  

* AtomicSimpleCpu : Αυτο το μοντελο χρησιμοποιει Atomic Memory Access. 
Χρησιμοποιει τον λανθανοντα χρονο για να υπολογισει τον συνολικο χρονο προσβασης της cache.
* MinorCPU  : Αυτο είναι ένα ευέλικτο μοντέλο in-order CPU που αναπτύχθηκε για να υποστηρίζει το Arm ISA. 
Εχει fixed four-stage in-order execution pipeline και διαθέτει ρυθμιζόμενες δομές δεδομένων και συμπεριφορά εκτέλεσης. 
Επομένως μπορεί να ρυθμιστεί για να μοντελοποιήσει συγκεκριμένο επεξεργαστή. 
Το four-stage pipeline περιλαμβάνει fetching lines, decomposition into macro-ops, decomposition of macro-ops into micro-ops και execute.
* TimingSimpleCPU: Αυτό ειναι ενα μοντέλο που χρησιμοποιεί Timing memory access, δηλαδή αναμενει το περας της προσπερασης της μνημης μεχρι να συνεχίσει. Θεωρειται ομως αυτά το TimingSimpleCPU είναι 
επίσης ένα fast-to-run μοντέλο, δεδομένου ότι απλοποιεί ορισμένες πτυχές όπως το pipelining
 πράγμα που σημαίνει ότι μόνο μία εντολή επεξεργάζεται κάθε στιγμή. Κάθε αριθμητική εντολή εκτελείται από το TimingSimpleCPU σε έναν μόνο κύκλο,
 ενώ οι προσπελάσεις στη μνήμη απαιτούν πολλαπλούς κύκλους.


_a_)  
Το πρόγραμμα C που φτιάξαμε ελέγχει αν ένας αριθμός είναι μονός ή ζυγός.

* MinorCPU: 
   * Line 12 : sim_seconds = 0.000039 # Number of seconds simulated

* TimingSimpleCPU:  
   * Line 12 : sim_seconds = 0.000046 # Number of seconds simulated

_b_)  
 Στην περίπτωση του TimingSimpleCPU ο χρόνος είναι μεγαλύτερος απο ότι στον MinorCPU καθώς 
στον TimingSimpleCPU, όπως ειπαμε παραπάνω περιμένει το πέρας της προσπέλασης της μνήμης 
μέχρι να συνεχίσει και επιπλέον δεν διαθέτει pipeline. Ενώ ο MinorCpu απο την άλλη έχει 
pipeline τεσσάρων σταδίων. Ένας ακόμα λόγος που ο MinorCPU έχει μικρότερο χρονο ειναι και ο
συνολικός αρθμός κύκλων των επεξεργαστών όπως φαίνεται παρακάτω.

* TimingSimpleCPU : 92742 # number of cpu cycles simulated
* MinorCPU        : 78854 # number of cpu cycles simulated

_c_)  
Αρχικά θα κάνουμε προσομείωση με συχνότητα 500MHz.  
* MinorCPU:
  * Line 12 : sim_seconds = 0.000046 # Number of seconds simulated
* TimingSimpleCPU:
  * Line 12 : sim_seconds = 0.000054 # Number of seconds simulated

Είναι ορατό πως οι χρόνοι αυξήθηκαν , πραάγμα λογικό καθώς μειώσαμε την συχνότητα των επεξεργαστών.

Στη συνέχεια αλλάζουμε την τεχνολογία της μνήμες σε DDR3_2133_8x8   

* MinorCPU:
  * Line 12 : sim_seconds = 0.000038 # Number of seconds simulated
* TimingSimpleCPU:
  * Line 12 : sim_seconds = 0.000045 # Number of seconds simulated

Παρατηρούμε ότι μειώθηκαν οι χρόνοι εκτέλεσης από την αρχική μας περίπτωση. Αυτό είναι λογικό αφού αρχικά είχαμε DDR3_1600_8x8 : (1.6 x 8 x 8 / 8 = 12.8 GBps) και τώρα DDR3_2133_8x8 : (2.133 x 8 x 8 / 8 = 17.0 GBps).
