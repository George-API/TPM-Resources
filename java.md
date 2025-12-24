/* ============================================================
   Java for Data Integration & Enterprise Backend — Core Syntax Ref
   ============================================================
   Focus: service/back-end patterns, integration, I/O, JSON, HTTP,
   SOAP, concurrency basics, validation, logging, DB access, errors.
   ============================================================ */

// =========================
// 4) Collections (integration payloads, headers, routing, batching)
// =========================

List<String> list = new ArrayList<>();                 // ordered batch of items (IDs, records, lines)
Set<String> set = new HashSet<>();                     // de-dup keys (idempotency keys, seen IDs)
Map<String, String> headers = new HashMap<>();         // metadata map (HTTP headers, routing/config)
List<String> imm = List.of("a", "b");                  // immutable config defaults
Map<String, Integer> immMap = Map.of("a", 1);          // immutable constants

Optional<String> opt = Optional.ofNullable(maybe);     // explicit “may be missing”
String v = opt.orElse("fallback");                     // default if missing


// =========================
// 5) Exceptions + Boundary-Focused Error Handling (integration-safe)
// =========================

try { /* ... */ } catch (RuntimeException e) { /* log + translate */ } finally { /* cleanup */ }
throw new IllegalArgumentException("bad input");       // reject invalid upstream payload early

class IntegrationException extends RuntimeException {   // translate low-level failures into integration meaning
  IntegrationException(String msg, Throwable cause) { super(msg, cause); }
}


// =========================
// 6) Logging (observability: trace integration flows end-to-end)
// =========================
// In production: SLF4J + Logback/Log4j2. Always log correlation IDs (requestId/eventId).

import java.util.logging.Logger;

class Service {
  private static final Logger LOG = Logger.getLogger(Service.class.getName());
  void run(String id) {
    LOG.info(() -> "start id=" + id);
    try { /* ... */ }
    catch (Exception e) { LOG.severe("failed id=" + id + " err=" + e.getMessage()); throw e; }
  }
}


// =========================
// 7) I/O + Try-With-Resources (always close files/streams safely)
// =========================

import java.io.*;
import java.nio.charset.StandardCharsets;
import java.nio.file.*;

Path in = Paths.get("data/in.txt");
String text = Files.readString(in, StandardCharsets.UTF_8);         // read small text
try (var lines = Files.lines(in, StandardCharsets.UTF_8)) {         // stream large text
  lines.filter(l -> !l.isBlank()).forEach(System.out::println);
}


// =========================
// 8) JSON Payloads (DTOs for APIs, events, queues)
// =========================

import com.fasterxml.jackson.databind.ObjectMapper;

class Json {
  private static final ObjectMapper MAPPER = new ObjectMapper();
  static <T> T fromJson(String json, Class<T> cls) {
    try { return MAPPER.readValue(json, cls); }
    catch (Exception e) { throw new IntegrationException("invalid json payload", e); }
  }
  static String toJson(Object obj) {
    try { return MAPPER.writeValueAsString(obj); }
    catch (Exception e) { throw new IntegrationException("json serialization failed", e); }
  }
}

record UserEvent(String userId, String action, Instant ts) {}


// =========================
// 9) HTTP Client (calling upstream/downstream services)
// =========================

import java.net.URI;
import java.net.http.*;
import java.time.Duration;

class Http {
  private final HttpClient client = HttpClient.newBuilder()
      .connectTimeout(Duration.ofSeconds(10))
      .build();

  String getJson(String url, Map<String, String> headers) {
    try {
      HttpRequest.Builder b = HttpRequest.newBuilder()
          .uri(URI.create(url))
          .timeout(Duration.ofSeconds(20))
          .GET();
      headers.forEach(b::header);

      HttpResponse<String> resp = client.send(b.build(), HttpResponse.BodyHandlers.ofString());
      if (resp.statusCode() / 100 != 2) throw new IntegrationException("HTTP " + resp.statusCode(), null);
      return resp.body();
    } catch (Exception e) {
      throw (e instanceof IntegrationException ie) ? ie : new IntegrationException("http call failed", e);
    }
  }
}


// =========================
// 10) SOAP Client (common enterprise integration)
// =========================
// Most common approach: generate strongly-typed stubs from WSDL (JAX-WS),
// then call port methods like normal Java methods.
//
// Typical generation (tooling varies by build):
//   wsimport -keep -s src/main/java -p com.example.soap https://host/service?wsdl
//
// In modern projects you usually use a Maven/Gradle plugin to generate sources.

import javax.xml.namespace.QName;
import javax.xml.ws.*;
import javax.xml.ws.handler.Handler;
import javax.xml.ws.handler.MessageContext;
import javax.xml.ws.handler.soap.*;
import javax.xml.soap.SOAPMessage;

// --- Basic JAX-WS call shape (generated classes assumed: MyService, MyPort, Request, Response)
class SoapClient {
  private final MyPort port;

  SoapClient(String endpointUrl) {
    MyService service = new MyService();              // generated Service subclass
    this.port = service.getMyPort();                  // generated Port interface

    BindingProvider bp = (BindingProvider) port;
    bp.getRequestContext().put(BindingProvider.ENDPOINT_ADDRESS_PROPERTY, endpointUrl); // override endpoint

    // Timeouts (common JAX-WS RI keys; other stacks may differ)
    bp.getRequestContext().put("com.sun.xml.ws.connect.timeout", 10_000);               // connect timeout ms
    bp.getRequestContext().put("com.sun.xml.ws.request.timeout", 20_000);               // read timeout ms

    // Optional: attach handler chain (for headers / correlation IDs / auth)
    Binding binding = bp.getBinding();
    List<Handler> chain = new ArrayList<>(binding.getHandlerChain());
    chain.add(new SoapHeaderHandler("X-Correlation-Id", UUID.randomUUID().toString()));
    binding.setHandlerChain(chain);
  }

  MyResponse call(MyRequest req) {
    try {
      return port.someOperation(req);                 // strongly-typed SOAP call
    } catch (SOAPFaultException e) {
      // SOAP Fault returned by server (structured error)
      throw new IntegrationException("soap fault: " + e.getFault().getFaultString(), e);
    } catch (WebServiceException e) {
      // transport / timeout / TLS / connection failures
      throw new IntegrationException("soap transport failed", e);
    }
  }
}

// --- SOAP header injection (common: auth tokens, correlation IDs, tenant IDs)
// Note: Header name here is illustrative; real SOAP headers often use QNames + namespaces.
class SoapHeaderHandler implements SOAPHandler<SOAPMessageContext> {
  private final String headerName;
  private final String headerValue;

  SoapHeaderHandler(String headerName, String headerValue) {
    this.headerName = headerName;
    this.headerValue = headerValue;
  }

  @Override public boolean handleMessage(SOAPMessageContext ctx) {
    Boolean outbound = (Boolean) ctx.get(MessageContext.MESSAGE_OUTBOUND_PROPERTY);
    if (Boolean.TRUE.equals(outbound)) {
      try {
        SOAPMessage msg = ctx.getMessage();
        var envelope = msg.getSOAPPart().getEnvelope();
        var header = envelope.getHeader();
        if (header == null) header = envelope.addHeader();
        header.addHeaderElement(new QName(headerName)).addTextNode(headerValue); // simple header
        msg.saveChanges();
      } catch (Exception e) {
        throw new IntegrationException("failed to add soap header", e);
      }
    }
    return true;
  }

  @Override public boolean handleFault(SOAPMessageContext ctx) { return true; }
  @Override public void close(MessageContext ctx) {}
  @Override public Set<QName> getHeaders() { return Collections.emptySet(); }
}


// =========================
// 11) Concurrency Basics (parallel integration work + timeouts)
// =========================

import java.util.concurrent.*;

ExecutorService pool = Executors.newFixedThreadPool(8); // bounded concurrency
Future<String> f = pool.submit(() -> "result");         // submit task
String res = f.get(10, TimeUnit.SECONDS);               // timeout to avoid hung pipelines
pool.shutdown();


// =========================
// 12) Database Access (JDBC fundamentals: parameterized SQL + safe closing)
// =========================

import java.sql.*;

class Db {
  Connection conn;                                      // typically from a DataSource/pool

  int exec(String sql, List<Object> params) {
    try (PreparedStatement ps = conn.prepareStatement(sql)) {
      for (int i = 0; i < params.size(); i++) ps.setObject(i + 1, params.get(i)); // 1-based
      return ps.executeUpdate();
    } catch (SQLException e) {
      throw new IntegrationException("db write failed", e);
    }
  }
}


// =========================
// 13) Validation (fail fast at boundaries: API input, events, config)
// =========================

static void requireNonBlank(String v, String field) {
  if (v == null || v.isBlank()) throw new IllegalArgumentException(field + " required");
}


// =========================
// 14) Minimal Integration Handler Skeleton (HTTP/SOAP -> parse -> validate -> persist)
// =========================

class IntegrationHandler {
  private final Http http = new Http();
  private final SoapClient soap = new SoapClient("https://soap-host/service");
  private static final Logger LOG = Logger.getLogger(IntegrationHandler.class.getName());

  void handleHttp(String url) {
    LOG.info(() -> "http start url=" + url);
    String json = http.getJson(url, Map.of("Accept", "application/json"));
    UserEvent evt = Json.fromJson(json, UserEvent.class);
    requireNonBlank(evt.userId(), "userId");
    LOG.info(() -> "http done userId=" + evt.userId());
  }

  void handleSoap(MyRequest req) {
    LOG.info(() -> "soap start op=someOperation");
    MyResponse resp = soap.call(req);
    LOG.info(() -> "soap done status=" + resp.getStatus());
  }
}
