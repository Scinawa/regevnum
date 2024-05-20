## Examples: Finding the order of $\mathbb Z_N^*$

### Prerequisites
Launch [Sage](https://www.sagemath.org) and load the [<code>order-finding.sage</code>](../order-finding.sage) script by executing:
```console
$ sage
(..)
sage: load("order-finding.sage")
```

### Simulating the quantum algorithm
To simulate order finding in $\mathbb Z_N^*$, for $N$ an integer of length $n$ bits with $t$ distinct prime factors, each of length $l$ bits, first setup such an integer on special form by executing:

```console
sage: [N, factors] = sample_integer(t = 2, l = 1024, verbose = True)
Sampling N on special form to enable efficient simulation...
 Sampled factor: 170036129814264621299003532851751454383489939818759603266036179344125657073535812422936960452869838752652009510028067225563279119831842982681737326962019574955765519848467315462557625993175582810147154176760333481983312343057681105736730649785524318471432019488237120454498687313377074997230460474258553379699
 Sampled factor: 90739047430626446012079861480118265807383404428982736737348310016476687687630950909008628740912209216161620887232807150245704276877510275253582556755926559979878408369581727317895321332607704892069467022187448923812664655113942697193097729287192925398412803130769152566754220286268078057831813745240374357999

 Sampled N = 15428916448136713018052909488898638158549277059422128593628988316780606152763451331935304748944545669838069184005022476142363355151242092218398913570980424254768671005273114911789051227414414302772505322888706611193702233806888135860485338390291845069107862742232392416370905938090566283040982371074435085652715267835037510903994313583410873559821722369324041178495714916520970054363679268627927487298813918796251057995485472699431791975611468966977469136384088815144037485205137544189378082382880894432902635394967458512271522152465491673570372763537926980956361970975988555704772361805721189439876996934618804862301

 Time required to sample: 136 ms 981 µs
sage: n = N.nbits()
```

This yields $N$ and the prime factors of $N$.

Next, to find the order $\varphi(N)$ of $\mathbb Z_N^*$, generate a basis for the $d$-dimensional lattice for this problem instance, where $d = \lceil \sqrt{n} \rceil$, by executing:

```console
sage: d = ceil(sqrt(n))
sage: B = generate_basis_for_order_finding(N, factors, d = d, verbose = True)
Generating a basis for the lattice L...
 Sampling vectors from L...
  Sampling vectors 1 to 8 of 54 using 8 threads...
  Sampling vectors 9 to 16 of 54 using 8 threads...
  Sampling vectors 17 to 24 of 54 using 8 threads...
  Sampling vectors 25 to 32 of 54 using 8 threads...
  Sampling vectors 33 to 40 of 54 using 8 threads...
  Sampling vectors 41 to 48 of 54 using 8 threads...
  Sampling vectors 49 to 54 of 54 using 8 threads...

 Reducing the basis for L, this may take a moment...

 Time required to generate a basis for L: 1 min 44 sec 766 ms 787 µs
```

This basis can then be used to setup the simulator by executing:

```console
sage: simulator = Simulator(B, verbose = True)
Setting up the simulator...
 Computing the basis for the dual L^* of L...
 Computing the Gram–Schmidt orthogonalization of the basis...

 Time required to setup the simulator: 9 sec 915 ms 601 µs
```

Finally, simulate running the quantum algorithm $d + 4$ times with $C = 2$ by executing:

```console
sage: R = get_regev_R(C = 2, n = N.nbits())
sage: samples = simulator.sample_vectors(R = R, verbose = True)
Sampling 50 good vectors and 0 bad vectors...
 Sampling vectors 1 to 8 of 50 using 8 threads...
 Sampling vectors 9 to 16 of 50 using 8 threads...
 Sampling vectors 17 to 24 of 50 using 8 threads...
 Sampling vectors 25 to 32 of 50 using 8 threads...
 Sampling vectors 33 to 40 of 50 using 8 threads...
 Sampling vectors 41 to 48 of 50 using 8 threads...
 Sampling vectors 49 to 50 of 50 using 2 threads...

 Time required to sample: 2 sec 925 ms 173 µs
```

### Executing the classical post-processing
To solve the samples for the order $\varphi(N)$ of $\mathbb Z_N^*$, execute:

```console
sage: phi_found = solve_samples_for_phi(samples, N, R, verbose = True)
Post-processing the sampled vectors to find phi...
 Building the post-processing matrix...
 Running LLL on the post-processing matrix...
  Found 46 / 46 linearly independent vectors...

 Found phi = 15428916448136713018052909488898638158549277059422128593628988316780606152763451331935304748944545669838069184005022476142363355151242092218398913570980424254768671005273114911789051227414414302772505322888706611193702233806888135860485338390291845069107862742232392416370905938090566283040982371074435085652454492657792619836683230189079003839630849025076298838492330427160367709602512505295981898105031870827437427598224598323622808578902115709042149252666142680208393556987088501408925135057097606730686014196019676106475545154293867870640544384465209737086517148356982282683519454206076036384814722715119877124604

 Time required to post-process: 12 sec 393 ms 852 µs
```

To use the post-processing from [[E21b]](https://doi.org/10.1007/s11128-021-03069-1) to completely factor the integer $N$ given $\varphi(N)$, execute:

```console
sage: factors = factor_completely(phi_found, N)
Solving for the complete factorization...

Iteration: 0
Found factors: 1
Found primes: 0
 Factor 0: 15428916448136713018052909488898638158549277059422128593628988316780606152763451331935304748944545669838069184005022476142363355151242092218398913570980424254768671005273114911789051227414414302772505322888706611193702233806888135860485338390291845069107862742232392416370905938090566283040982371074435085652715267835037510903994313583410873559821722369324041178495714916520970054363679268627927487298813918796251057995485472699431791975611468966977469136384088815144037485205137544189378082382880894432902635394967458512271522152465491673570372763537926980956361970975988555704772361805721189439876996934618804862301

Iteration: 1
Found factors: 1
Found primes: 0
 Factor 0: 15428916448136713018052909488898638158549277059422128593628988316780606152763451331935304748944545669838069184005022476142363355151242092218398913570980424254768671005273114911789051227414414302772505322888706611193702233806888135860485338390291845069107862742232392416370905938090566283040982371074435085652715267835037510903994313583410873559821722369324041178495714916520970054363679268627927487298813918796251057995485472699431791975611468966977469136384088815144037485205137544189378082382880894432902635394967458512271522152465491673570372763537926980956361970975988555704772361805721189439876996934618804862301

Iteration: 2
Found factors: 1
Found primes: 0
 Factor 0: 15428916448136713018052909488898638158549277059422128593628988316780606152763451331935304748944545669838069184005022476142363355151242092218398913570980424254768671005273114911789051227414414302772505322888706611193702233806888135860485338390291845069107862742232392416370905938090566283040982371074435085652715267835037510903994313583410873559821722369324041178495714916520970054363679268627927487298813918796251057995485472699431791975611468966977469136384088815144037485205137544189378082382880894432902635394967458512271522152465491673570372763537926980956361970975988555704772361805721189439876996934618804862301

Iteration: 3
Found factors: 1
Found primes: 0
 Factor 0: 15428916448136713018052909488898638158549277059422128593628988316780606152763451331935304748944545669838069184005022476142363355151242092218398913570980424254768671005273114911789051227414414302772505322888706611193702233806888135860485338390291845069107862742232392416370905938090566283040982371074435085652715267835037510903994313583410873559821722369324041178495714916520970054363679268627927487298813918796251057995485472699431791975611468966977469136384088815144037485205137544189378082382880894432902635394967458512271522152465491673570372763537926980956361970975988555704772361805721189439876996934618804862301

Iteration: 4
Found factors: 1
Found primes: 0
 Factor 0: 15428916448136713018052909488898638158549277059422128593628988316780606152763451331935304748944545669838069184005022476142363355151242092218398913570980424254768671005273114911789051227414414302772505322888706611193702233806888135860485338390291845069107862742232392416370905938090566283040982371074435085652715267835037510903994313583410873559821722369324041178495714916520970054363679268627927487298813918796251057995485472699431791975611468966977469136384088815144037485205137544189378082382880894432902635394967458512271522152465491673570372763537926980956361970975988555704772361805721189439876996934618804862301

Iteration: 5
Found factors: 2
Found primes: 2
 Factor 0: 90739047430626446012079861480118265807383404428982736737348310016476687687630950909008628740912209216161620887232807150245704276877510275253582556755926559979878408369581727317895321332607704892069467022187448923812664655113942697193097729287192925398412803130769152566754220286268078057831813745240374357999
 Factor 1: 170036129814264621299003532851751454383489939818759603266036179344125657073535812422936960452869838752652009510028067225563279119831842982681737326962019574955765519848467315462557625993175582810147154176760333481983312343057681105736730649785524318471432019488237120454498687313377074997230460474258553379699

Time required to solve: 27 ms 137 µs
 Time spent exponentiating: 20 ms 752 µs
 Time spent checking primality: 5 ms 492 µs
 Time spent reducing perfect powers: 218 µs
sage: print(f"Found the following collection of factors:\n{factors}")
Found the following collection of factors:
{170036129814264621299003532851751454383489939818759603266036179344125657073535812422936960452869838752652009510028067225563279119831842982681737326962019574955765519848467315462557625993175582810147154176760333481983312343057681105736730649785524318471432019488237120454498687313377074997230460474258553379699, 90739047430626446012079861480118265807383404428982736737348310016476687687630950909008628740912209216161620887232807150245704276877510275253582556755926559979878408369581727317895321332607704892069467022187448923812664655113942697193097729287192925398412803130769152566754220286268078057831813745240374357999}
sage: print(f"Found the complete factorization: {factors.is_complete()}")
Found the complete factorization: True
```

For further details on this post-processing, see [[E21b]](https://doi.org/10.1007/s11128-021-03069-1), the [dependencies](../dependencies) directory and the [factoritall](https://github.com/ekera/factoritall) repository. Note that this post-processing multiplies on small factors to the order found; this is not necessary when factoring given $\varphi(N)$, but at the same time it does not hurt; it only slightly increases the time required for the post-processing.

### Convenience test function
There is a also a convenience test function that performs the above order-finding procedure given $t$ and $l$.

To try it out, execute:

```console
sage: result = test_order_finding_phi(t = 8, l = 32, verbose = True)
** Setting up the problem instance...

Sampling N on special form to enable efficient simulation...
 Sampled factor: 3291013499
 Sampled factor: 2309542679
 Sampled factor: 2835426743
 Sampled factor: 3135430919
 Sampled factor: 2161570823
 Sampled factor: 3765336227
 Sampled factor: 2271428099
 Sampled factor: 3981664223

 Sampled N = 4974027509353495278910429694691075794117618812238308022753369929494133304269

 Time required to sample: 56 ms 413 µs

** Setting up the simulator...

Generating a basis for the lattice L...
 Sampling vectors from L...
  Sampling vectors 1 to 8 of 24 using 8 threads...
  Sampling vectors 9 to 16 of 24 using 8 threads...
  Sampling vectors 17 to 24 of 24 using 8 threads...

 Reducing the basis for L, this may take a moment...

 Time required to generate a basis for L: 442 ms 980 µs

Setting up the simulator...
 Computing the basis for the dual L^* of L...
 Computing the Gram–Schmidt orthogonalization of the basis...

 Time required to setup the simulator: 36 ms 989 µs

** Sampling vectors...

Sampling 20 good vectors and 0 bad vectors...
 Sampling vectors 1 to 8 of 20 using 8 threads...
 Sampling vectors 9 to 16 of 20 using 8 threads...
 Sampling vectors 17 to 20 of 20 using 4 threads...

 Time required to sample: 118 ms 288 µs

** Solving for phi...

Post-processing the sampled vectors to find phi...
 Building the post-processing matrix...
 Running LLL on the post-processing matrix...
  Found 16 / 16 linearly independent vectors...

 Found phi = 4974027495286597679915275383931432438026903160436235004478346971409290261248

 Time required to post-process: 60 ms 456 µs
sage: print(f"Found the expected order: {result}")
Found the expected order: True
```

Note that there are many optional parameters that can be passed to the above convenience function, and to the functions it in turn calls, allowing the simulation, sampling and post-processing to be tweaked in various ways. For further details, please see the source code documentation for the aforementioned functions.

## Examples: Finding the order of $g \in \mathbb Z_N^*$

### Simulating the quantum algorithm
To simulate order finding in $\mathbb Z_N^*$, for $N$ an integer of length $n$ bits with $t$ distinct prime factors, each of length $l$ bits, first setup such an integer on special form by executing:

```console
sage: [N, factors] = sample_integer(t = 2, l = 1024, verbose = True)
Sampling N on special form to enable efficient simulation...
 Sampled factor: 132005690324535314515971814706793942875573035098140897976253373426529255353148073139728288907006122938770691532517368906753246553924568826734588495577124451453737952607157498658966860221994628679941747973613897727186209539363839173316567394164575418322501935106134039606553951496820896956115264523461562604823
 Sampled factor: 116989897785634351547953265340867446005402023473814388967420828811566158378862320918301821619470593117807645568806851079882333976467095233954855844958040323110690157703971010201944205769344965232564075392578861916590407918734208376457050557146430920633316199795618167667928163867824990411762323868105987647639

 Sampled N = 15443332218189487936842377710691782074634499944129793830811149159231038242593281007502719483805375443954400863097529415574300116507516233329103446583072092622568329435683135470347329276201088002836548817210083832295045229930080061502193393742966438306843395021056174506818287574408889781212725963391159058301034778680650537921561395550151932980342874157810900225247148454383734067721445465575638633818973741403055868418021225716971463371701207656129285843447279735535606690329988325004198649842350203785041073059291236513700260570692087443278693993216345079030594834985060350783746767241224809067746161284319125962897

 Time required to sample: 263 ms 895 µs
sage: n = N.nbits()
```

This yields $N$ and the prime factors of $N$.

To pick $g$ uniformly at random from $\mathbb Z_N^*$, execute:

```console
sage: g = IntegerModRing(N).random_element()
```

Next, to find the order $r$ of $g \in \mathbb Z_N^*$, generate a basis for the $d$-dimensional lattice for this problem instance, where $d = \lceil \sqrt{n} \rceil$, by executing:

```console
sage: d = ceil(sqrt(n))
sage: B = generate_basis_for_order_finding(N, factors, u = [g], d = d, verbose = True)
Generating a basis for the lattice L...
 Sampling vectors from L...
  Sampling vectors 1 to 8 of 54 using 8 threads...
  Sampling vectors 9 to 16 of 54 using 8 threads...
  Sampling vectors 17 to 24 of 54 using 8 threads...
  Sampling vectors 25 to 32 of 54 using 8 threads...
  Sampling vectors 33 to 40 of 54 using 8 threads...
  Sampling vectors 41 to 48 of 54 using 8 threads...
  Sampling vectors 49 to 54 of 54 using 8 threads...

 Reducing the basis for L, this may take a moment...

 Time required to generate a basis for L: 1 min 59 sec 567 ms 39 µs
```

This basis can then be used to setup the simulator by executing:

```console
sage: simulator = Simulator(B, verbose = True)
Setting up the simulator...
 Computing the basis for the dual L^* of L...
 Computing the Gram–Schmidt orthogonalization of the basis...

 Time required to setup the simulator: 7 sec 639 ms 857 µs
```

Finally, simulate running the quantum algorithm $d + 4$ times with $C = 2$ by executing:

```console
sage: R = get_regev_R(C = 2, n = N.nbits())
sage: samples = simulator.sample_vectors(R = R, verbose = True)
Sampling 50 good vectors and 0 bad vectors...
 Sampling vectors 1 to 8 of 50 using 8 threads...
 Sampling vectors 9 to 16 of 50 using 8 threads...
 Sampling vectors 17 to 24 of 50 using 8 threads...
 Sampling vectors 25 to 32 of 50 using 8 threads...
 Sampling vectors 33 to 40 of 50 using 8 threads...
 Sampling vectors 41 to 48 of 50 using 8 threads...
 Sampling vectors 49 to 50 of 50 using 2 threads...

 Time required to sample: 2 sec 52 ms 227 µs
```

### Executing the classical post-processing
To solve the samples for the order $r$ of $g \in \mathbb Z_N^*$, execute:

```console
sage: r_found = solve_samples_for_order(samples, g, N, R, verbose = True)
Post-processing the sampled vectors to find the order...
 Building the post-processing matrix...
 Running LLL on the post-processing matrix...
  Found 46 / 46 linearly independent vectors...

 Found r = 7721666109094743968421188855345891037317249972064896915405574579615519121296640503751359741902687721977200431548764707787150058253758116664551723291536046311284164717841567735173664638100544001418274408605041916147522614965040030751096696871483219153421697510528087253409143787204444890606362981695579529150392891546270184127748735235052135795730949549619472469151737126072819326994717535758804261646248512673238765658348502865167941420654771797719920751456057480485589290009429908071643791925505304936267624846549238434961821556297019946752538020952669370037388350041654071754632325938289460849934286446375787855218

 Time required to post-process: 12 sec 241 ms 801 µs
```

To use the post-processing from [[E21b]](https://doi.org/10.1007/s11128-021-03069-1) to completely factor the integer $N$ given $r$, execute:

```console
sage: factors = factor_completely(r_found, N)
Solving for the complete factorization...

Iteration: 0
Found factors: 1
Found primes: 0
 Factor 0: 15443332218189487936842377710691782074634499944129793830811149159231038242593281007502719483805375443954400863097529415574300116507516233329103446583072092622568329435683135470347329276201088002836548817210083832295045229930080061502193393742966438306843395021056174506818287574408889781212725963391159058301034778680650537921561395550151932980342874157810900225247148454383734067721445465575638633818973741403055868418021225716971463371701207656129285843447279735535606690329988325004198649842350203785041073059291236513700260570692087443278693993216345079030594834985060350783746767241224809067746161284319125962897

Iteration: 1
Found factors: 1
Found primes: 0
 Factor 0: 15443332218189487936842377710691782074634499944129793830811149159231038242593281007502719483805375443954400863097529415574300116507516233329103446583072092622568329435683135470347329276201088002836548817210083832295045229930080061502193393742966438306843395021056174506818287574408889781212725963391159058301034778680650537921561395550151932980342874157810900225247148454383734067721445465575638633818973741403055868418021225716971463371701207656129285843447279735535606690329988325004198649842350203785041073059291236513700260570692087443278693993216345079030594834985060350783746767241224809067746161284319125962897

Iteration: 2
Found factors: 2
Found primes: 2
 Factor 0: 116989897785634351547953265340867446005402023473814388967420828811566158378862320918301821619470593117807645568806851079882333976467095233954855844958040323110690157703971010201944205769344965232564075392578861916590407918734208376457050557146430920633316199795618167667928163867824990411762323868105987647639
 Factor 1: 132005690324535314515971814706793942875573035098140897976253373426529255353148073139728288907006122938770691532517368906753246553924568826734588495577124451453737952607157498658966860221994628679941747973613897727186209539363839173316567394164575418322501935106134039606553951496820896956115264523461562604823

Time required to solve: 14 ms 65 µs
 Time spent exponentiating: 8 ms 282 µs
 Time spent checking primality: 5 ms 209 µs
 Time spent reducing perfect powers: 187 µs
sage: print(f"Found the following collection of factors:\n{factors}")
Found the following collection of factors:
{132005690324535314515971814706793942875573035098140897976253373426529255353148073139728288907006122938770691532517368906753246553924568826734588495577124451453737952607157498658966860221994628679941747973613897727186209539363839173316567394164575418322501935106134039606553951496820896956115264523461562604823, 116989897785634351547953265340867446005402023473814388967420828811566158378862320918301821619470593117807645568806851079882333976467095233954855844958040323110690157703971010201944205769344965232564075392578861916590407918734208376457050557146430920633316199795618167667928163867824990411762323868105987647639}
sage: print(f"Found the complete factorization: {factors.is_complete()}")
Found the complete factorization: True
```

For further details on this post-processing, see [[E21b]](https://doi.org/10.1007/s11128-021-03069-1), the [dependencies](../dependencies) directory and the [factoritall](https://github.com/ekera/factoritall) repository.

### Convenience test function
There is a also a convenience test function that performs the above order-finding procedure given $t$ and $l$.

To try it out, execute:

```console
sage: result = test_order_finding(t = 8, l = 32, verbose = True)
** Setting up the problem instance...

Sampling N on special form to enable efficient simulation...
 Sampled factor: 2339987303
 Sampled factor: 2369958167
 Sampled factor: 2183231627
 Sampled factor: 2987282939
 Sampled factor: 2591696579
 Sampled factor: 2953416527
 Sampled factor: 2678761223
 Sampled factor: 3137168963

 Sampled N = 2326543211777972560534974497008263286960592774817763118851820722473141358201

 Time required to sample: 56 ms 305 µs

Sampling a generator...
 Sampled g = 1291762270980753843600420579392251815884788586102381750248069740791492436284

** Setting up the simulator...

Generating a basis for the lattice L...
 Sampling vectors from L...
  Sampling vectors 1 to 8 of 24 using 8 threads...
  Sampling vectors 9 to 16 of 24 using 8 threads...
  Sampling vectors 17 to 24 of 24 using 8 threads...

 Reducing the basis for L, this may take a moment...

 Time required to generate a basis for L: 362 ms 440 µs

Setting up the simulator...
 Computing the basis for the dual L^* of L...
 Computing the Gram–Schmidt orthogonalization of the basis...

 Time required to setup the simulator: 35 ms 490 µs

** Sampling vectors...

Sampling 20 good vectors and 0 bad vectors...
 Sampling vectors 1 to 8 of 20 using 8 threads...
 Sampling vectors 9 to 16 of 20 using 8 threads...
 Sampling vectors 17 to 20 of 20 using 4 threads...

 Time required to sample: 109 ms 331 µs

** Solving for the order...

Post-processing the sampled vectors to find the order...
 Building the post-processing matrix...
 Running LLL on the post-processing matrix...
  Found 16 / 16 linearly independent vectors...

 Found r = 18176118786422038204292239786587435199160829228383589408763630953631413674

 Time required to post-process: 57 ms 435 µs
sage: print(f"Found the expected order: {result}")
Found the expected order: True
```

Note that there are many optional parameters that can be passed to the above convenience function, and to the functions it in turn calls, allowing the simulation, sampling and post-processing to be tweaked in various ways. For further details, please see the source code documentation for the aforementioned functions.