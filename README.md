# Notice
This is a fork from https://github.com/mykter/afl-training which contains the material from "Fuzzing with AFL" workshop by Micheal Macnair (@micheal_macnair). The workshop has been completed and should only require following the steps below. 

The LICENSE and README (content below the instructions) header have been left as they were when forking.

These changes have been made in order to produce results demonstrating that issues such as Heartbleed may be found through fuzzing code. 

# Instructions

This is adapted from the libFuzzer example here: https://github.com/google/fuzzer-test-suite/tree/master/openssl-1.0.1f

- Get the openSSL source for version OpenSSL_1_0_1f:

    git submodule init
    git submodule update

- Configure and build with ASAN:

		cd openssl
		CC=afl-clang-fast CXX=afl-clang-fast++ ./config -d
		AFL_USE_ASAN=1 make
(note you can do "make -j" for faster builds, but there is a race that makes this fail occasionally)

Now fix up the code in handshake.cc to work with afl.  (or copy it out of ANSWERS.md!)

Build our target:

	AFL_USE_ASAN=1 afl-clang-fast++ -g handshake.cc openssl/libssl.a openssl/libcrypto.a -o handshake -I openssl/include -ldl


# Running

The following has been copy pasted from a file original called hints.

Because we're using ASAN, we need to run this in a slightly different way, see docs/notes_for_asan.txt:

	sudo ~/afl-2.52b/experimental/asan_cgroups/limit_memory.sh -u fuzzer afl-fuzz -i in -o out -m none ./handshake

An alternative is to not use the limit_memory script, and instead pass `-m none` to afl-fuzz. This runs the risk of the target allocating a huge amount of memory, and Linux will then start killing processes underneath you.



The following is the line used to start fuzzing and collect the data presented in our slides:

	afl-fuzz -i in -o out -m none ./handshake

