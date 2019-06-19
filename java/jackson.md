#### Jackson format double to string

```java
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.node.ArrayNode;
import com.fasterxml.jackson.databind.node.ObjectNode;
import com.fasterxml.jackson.databind.node.TextNode;

import java.io.IOException;
import java.util.Iterator;
import java.util.Map;

/**
 * JSON相关的处理
 *
 * @author vae
 */
public class JsonUtil {

	public static <T> JsonNode toJsonNode(T t) throws IOException {
		ObjectMapper mapper = new ObjectMapper();
		String json = mapper.writeValueAsString(t);
		return mapper.readTree(json);
	}

	public static <T> Object formatDouble(T t) {
		if (t != null) {
			try {
				JsonNode node = toJsonNode(t);
				doubleToString(node);
				return node;
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		return null;
	}

	private static void doubleToString(JsonNode node) {
		if (node.isObject()) {
			Iterator<Map.Entry<String, JsonNode>> it = node.fields();
			while (it.hasNext()) {
				Map.Entry<String, JsonNode> entry = it.next();
				if (entry.getValue().isObject() || entry.getValue().isArray()) {
					doubleToString(entry.getValue());
				} else if (entry.getValue().isDouble()) {
					((ObjectNode) node).put(entry.getKey(), StringUtil.formatTwoDecimal(entry.getValue().doubleValue()));
				}
			}
		}
		if (node.isArray()) {
			ArrayNode arrayNode = (ArrayNode) node;
			for (int i = 0; i < node.size(); i++) {
				JsonNode jsonNode = node.get(i);
				if (jsonNode.isObject() || jsonNode.isArray()) {
					doubleToString(jsonNode);
				} else if (jsonNode.isDouble()) {
					TextNode textNode = new TextNode(StringUtil.formatTwoDecimal(jsonNode.doubleValue()));
					arrayNode.set(i, textNode);
				}
				doubleToString(jsonNode);
			}
		}
	}
}
```

