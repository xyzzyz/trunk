licenses(["notice"])

# external/http_parser
cc_library(
  name = "external_http_parser",
  includes = [
    "upstream",
  ],
  hdrs = [
    "upstream/proxygen/external/http_parser/http_parser.h",
  ],
  srcs = [
    "upstream/proxygen/external/http_parser/http_parser.c",
    "upstream/proxygen/external/http_parser/http_parser_cpp.cpp",
  ]
)

# proxygen/httpserver
cc_library(
  name = "httpserver",
  srcs = glob([
    "upstream/proxygen/httpserver/*.cpp",
  ]),
  deps = [
    ":http"
  ]
)

# proxygen/lib/http
genrule(
  name = "gen_http_common_headers",
  srcs = [
    "upstream/proxygen/lib/http/gen_HTTPCommonHeaders.h.sh",
    "upstream/proxygen/lib/http/gen_HTTPCommonHeaders.cpp.sh",
    "upstream/proxygen/lib/http/HTTPCommonHeaders.template.gperf",
    "upstream/proxygen/lib/http/HTTPCommonHeaders.txt",
  ],
  cmd = "mkdir -p $(GENDIR)/third_party/proxygen/http/proxygen/lib/http && " +
    "HEADERS_LIST=$(location upstream/proxygen/lib/http/HTTPCommonHeaders.txt) " +
    "INSTALL_DIR=$(GENDIR)/third_party/proxygen/http/proxygen/lib/http " +
    "FBCODE_DIR=third_party/proxygen/upstream " + 
    "$(location upstream/proxygen/lib/http/gen_HTTPCommonHeaders.h.sh) && " +
    "HEADERS_LIST=$(location upstream/proxygen/lib/http/HTTPCommonHeaders.txt) " +
    "INSTALL_DIR=$(GENDIR)/third_party/proxygen/http/proxygen/lib/http " +
    "FBCODE_DIR=third_party/proxygen/upstream " + 
    "$(location upstream/proxygen/lib/http/gen_HTTPCommonHeaders.cpp.sh) ",

  outs = [
    "http/proxygen/lib/http/HTTPCommonHeaders.h",
    "http/proxygen/lib/http/HTTPCommonHeaders.cpp",
  ]
)

cc_library(
  name = "http",
  copts = [
  ],
  includes = [
    "upstream",
    "http",  # bazel also search s $(GENDIR)/third_party/proxygen/http
  ],
  srcs = glob([
    "upstream/proxygen/lib/http/*.cpp",
    "upstream/proxygen/lib/http/codec/*.cpp",
    "upstream/proxygen/lib/http/codec/compress/*.cpp",
    "upstream/proxygen/lib/http/session/*.cpp",
  ]) + [
    ":gen_http_common_headers",
  ],
  deps = [
    ":utils",
    ":service",
    "//third_party/boost:heap",
    "//third_party/boost:variant",
    "//third_party/folly",
    "//third_party/folly:async",
  ],
)

# proxygen/lib/services
cc_library(
  name = "service",
  srcs = glob([
    "upstream/proxygen/lib/services/*.cpp",
  ]),
  includes = [
    "upstream",
  ],
  deps = [
    "//third_party/folly:async",
  ]
)

# proxygen/lib/utils
genrule(
  name = "gen_trace_event_constants",
  srcs = [
    "upstream/proxygen/lib/utils/gen_trace_event_constants.py",
    "upstream/proxygen/lib/utils/TraceEventType.txt",
    "upstream/proxygen/lib/utils/TraceFieldType.txt",
  ],
  cmd = "mkdir -p $(GENDIR)/third_party/proxygen/trace && " +
    "python $(location upstream/proxygen/lib/utils/gen_trace_event_constants.py) " +
    "--output_type=cpp " +
    "--input_files=upstream/proxygen/lib/utils/TraceEventType.txt,upstream/proxygen/lib/utils/TraceFieldType.txt " +
    "--output_scope=proxygen " +
    "--header_path=proxygen/lib/utils " +
    "--fbcode_dir=. " + 
    "--install_dir=$(GENDIR)/third_party/proxygen/trace/proxygen/lib/utils",
  outs = [
    "trace/proxygen/lib/utils/TraceEventType.h",
    "trace/proxygen/lib/utils/TraceEventType.cpp",
    "trace/proxygen/lib/utils/TraceFieldType.h",
    "trace/proxygen/lib/utils/TraceFieldType.cpp",
  ]
)
  
cc_library(
  name = "utils",
  copts = [
    "-I$(GENDIR)/third_party/proxygen/trace",
  ],
  includes = [
    "upstream",
  ],
  srcs = [
    "upstream/proxygen/lib/utils/AsyncTimeoutSet.cpp",
    "upstream/proxygen/lib/utils/CryptUtil.cpp",
    "upstream/proxygen/lib/utils/Exception.cpp",
    "upstream/proxygen/lib/utils/HTTPTime.cpp",
    "upstream/proxygen/lib/utils/Logging.cpp",
    "upstream/proxygen/lib/utils/TraceEventContext.cpp",
    "upstream/proxygen/lib/utils/TraceEvent.cpp",
    ":gen_trace_event_constants",
  ],
  deps = [
    "//third_party/folly",
    "//third_party/folly:async",
    "//external:libssl",
  ],
)

cc_library(
  name = "ParseURL",
  srcs = [
    "upstream/proxygen/lib/utils/ParseURL.cpp"
  ],
  deps = [
    ":utils",
    ":external_http_parser",
  ]
)

cc_test(
  name = "CryptUtilTest",
  srcs = [
    "upstream/proxygen/lib/utils/test/CryptUtilTest.cpp",
  ],
  deps = [
    "//third_party/gtest:gtest_main",
    ":utils"
  ]
)

cc_test(
  name = "ParseURLTest",
  srcs = [
    "upstream/proxygen/lib/utils/test/ParseURLTest.cpp",
  ],
  deps = [
    "//third_party/gtest:gtest_main",
    ":ParseURL"
  ]
)

# Sample code
cc_library(
  name = "echo_handler",
  testonly = 1,
  hdrs = [
    "upstream/proxygen/httpserver/samples/echo/EchoHandler.h",
    "upstream/proxygen/httpserver/samples/echo/EchoStats.h",
  ],
  srcs = [
    "upstream/proxygen/httpserver/samples/echo/EchoHandler.cpp",
  ],
  deps = [
    ":http",
  ],
)

cc_binary(
  name = "echo_server",
  testonly = 1,
  srcs = [
    "upstream/proxygen/httpserver/samples/echo/EchoServer.cpp",
  ],
  deps = [
    ":httpserver",
    ":echo_handler",
  ],
)  

