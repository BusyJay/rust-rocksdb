SHELL=/bin/bash -o pipefail

${LOCAL_DIR}/lib/libgflags.a:
	cd /tmp && \
	curl -L https://github.com/gflags/gflags/archive/v2.1.2.tar.gz -o gflags.tar.gz && \
	tar xf gflags.tar.gz && \
	cd gflags-2.1.2 && \
	cmake -DCMAKE_INSTALL_PREFIX=${LOCAL_DIR} . && \
	make && \
	make install

${LOCAL_DIR}/lib/librocksdb.dylib: |prepare_brew
${LOCAL_DIR}/lib/librocksdb.so: ${LOCAL_DIR}/lib/libgflags.a
${LOCAL_DIR}/lib/librocksdb.dylib ${LOCAL_DIR}/lib/librocksdb.so:
	export CPLUS_INCLUDE_PATH="${CPLUS_INCLUDE_PATH}:${LOCAL_DIR}/include" && \
	export CXX=${COMPILER} && \
	cd /tmp && \
	curl -L https://github.com/facebook/rocksdb/archive/4.12.fb.tar.gz -o rocksdb.tar.gz && \
	tar xf rocksdb.tar.gz && \
	cd rocksdb-4.12.fb && \
	INSTALL_PATH=${LOCAL_DIR} make install-shared

ifeq ($(TRAVIS_OS_NAME),linux)
GO_ARCH = linux
else
GO_ARCH = darwin
endif

prepare_linux_dynamic: ${LOCAL_DIR}/lib/librocksdb.so

prepare_brew:
	brew update && \
	brew install gflags snappy

prepare_osx_dynamic: ${LOCAL_DIR}/lib/librocksdb.dylib | prepare_brew

test_linux_dynamic test_osx_dynamic:
	RUST_BACKTRACE=1 RUSTFLAGS=-Dwarnings cargo test

prepare_osx_static prepare_linux_static:

test_linux_static test_osx_static:
	env ROCKSDB_SYS_STATIC=1 CXX=${COMPILER} RUST_BACKTRACE=1 RUSTFLAGS=-Dwarnings cargo test

