set(
  source_files
  Args.cpp
  AtomicFile.cpp
  CacheEntryReader.cpp
  CacheEntryWriter.cpp
  CacheFile.cpp
  Compression.cpp
  Compressor.cpp
  Config.cpp
  Context.cpp
  Counters.cpp
  Decompressor.cpp
  Hash.cpp
  Lockfile.cpp
  Logging.cpp
  Manifest.cpp
  MiniTrace.cpp
  NullCompressor.cpp
  NullDecompressor.cpp
  ProgressBar.cpp
  Result.cpp
  ResultDumper.cpp
  ResultExtractor.cpp
  ResultRetriever.cpp
  SignalHandler.cpp
  Stat.cpp
  Statistics.cpp
  TemporaryFile.cpp
  ThreadPool.cpp
  Util.cpp
  ZstdCompressor.cpp
  ZstdDecompressor.cpp
  argprocessing.cpp
  ccache.cpp
  cleanup.cpp
  compopt.cpp
  compress.cpp
  execute.cpp
  hashutil.cpp
  language.cpp
  version.cpp)

if(INODE_CACHE_SUPPORTED)
  list(APPEND source_files InodeCache.cpp)
endif()

if(WIN32)
  list(APPEND source_files Win32Util.cpp)
endif()

add_library(ccache_lib STATIC ${source_files})

if(WIN32)
  target_link_libraries(ccache_lib PRIVATE ws2_32 "psapi")

  if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    target_link_libraries(
      ccache_lib PRIVATE -static gcc stdc++ winpthread -dynamic)
  elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    target_link_libraries(ccache_lib PRIVATE -static c++ -dynamic)
  endif()
endif()

find_package(Threads REQUIRED)
target_link_libraries(
  ccache_lib
  PRIVATE standard_settings standard_warnings ZSTD::ZSTD
          Threads::Threads third_party_lib)

target_include_directories(ccache_lib PRIVATE ${CMAKE_BINARY_DIR} .)

add_subdirectory(third_party)
