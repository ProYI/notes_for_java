### Objects

```java
package vip.proyi.guava.util;

import com.google.common.base.MoreObjects;
import com.google.common.base.Objects;
import com.google.common.collect.ComparisonChain;

import java.util.Calendar;

public class ObjectsTest {
    static class Guava implements Comparable<Guava> {
        private final String manufacturer;
        private final String version;
        private final Calendar releaseDate;

        public Guava(String manufacturer, String version, Calendar releaseDate) {
            this.manufacturer = manufacturer;
            this.version = version;
            this.releaseDate = releaseDate;
        }

        /*@Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            Guava guava = (Guava) o;
            return Objects.equals(manufacturer, guava.manufacturer) &&
                    Objects.equals(version, guava.version) &&
                    Objects.equals(releaseDate, guava.releaseDate);
        }

        @Override
        public int hashCode() {
            return Objects.hash(manufacturer, version, releaseDate);
        }

        @Override
        public String toString() {
            return "Guava{" +
                    "manufacturer='" + manufacturer + '\'' +
                    ", version='" + version + '\'' +
                    ", releaseDate=" + releaseDate +
                    '}';
        }*/

        @Override
        public String toString() {
            return MoreObjects.toStringHelper(this).omitNullValues()
                    .add("manufacturer", this.manufacturer)
                    .add("version", version)
                    .add("releaseDate", releaseDate).toString();
        }

        @Override
        public int hashCode() {
            return Objects.hashCode(manufacturer, version, releaseDate);
        }

        @Override
        public boolean equals(Object obj) {
            if (this == obj) return true;
            if (obj == null || getClass() != obj.getClass()) return false;
            Guava guava = (Guava) obj;

            return Objects.equal(this.manufacturer, guava.manufacturer)
                    && Objects.equal(this.version, guava.version)
                    && Objects.equal(this.releaseDate, guava.releaseDate);
        }

        @Override
        public int compareTo(Guava o) {
            return ComparisonChain.start().compare(this.manufacturer, o.manufacturer)
                    .compare(this.version, o.version).compare(this.releaseDate, o.releaseDate).result();
        }
    }
}

```

